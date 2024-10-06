created at : 2024-09-22 22:07

#### tags

#

--- 

## 1. 서론

ConcurrentHashMap의 동작 원리를 학습하던 중 Lock Stripping에 대한 키워드를 발견했다. Lock Stripping은 어떤 원리일까? 그리고 Java 8 이후에 바뀐 ConcurrentHashMap은 어떤 원리가 적용되었을까?

## 2. ConcurrentHashMap에 적용된 원리들

ConcurrentHashMap은 멀티 스레드 환경에서 안전하게 사용할 수 있는 HashMap이다. HashMap은 멀티 스레드 환경에서 안전성을 보장하지 않기 때문에 여러 스레드가 읽기-쓰기 작업을 할 경우 문제가 발생할 수 있다. ConcurrentHashMap에는 어떤 원리가 적용되었을까?

### 2.1 Synchronized 키워드

자바의 synchronized 키워드는 **모니터(Monitor)**로 동작한다.

모니터란 공유자원에 접근할 때 오직 하나의 스레드만 접근할 수 있고, 나머지 스레드는 진입 큐, 대기 큐에서 대기 상태에 놓인다. 이 때 조건 변수가 만족되기 전까지는 다른 스레드들은 대기 상태에 존재하고, 다른 스레드가 조건을 만족할 때 다른 스레드를 깨워서 공유 자원에 접근할 수 있다.

자바에서는 **synchronized 메서드**를 사용해 특정 메서드에만 모니터 락을 걸 수 있고 **synchronzied 블록**을 사용해서 특정 블록에만 모니터 락을 걸 수도 있다.

![그림1. 자바에서 monitor 구현 (출처 : 위키피디아)](https://prod-files-secure.s3.us-west-2.amazonaws.com/3eb500f6-ee39-48be-bde3-905c2bc684b1/c01c62f0-a06f-44c0-a2aa-b0c08b6649ba/302px-Monitor_(synchronization)-Java.png)

그림1. 자바에서 monitor 구현 (출처 : [위키피디아](https://en.wikipedia.org/wiki/Monitor_(synchronization)#Implicit_condition_variable_monitors))

하지만 공유 자원에는 오직 락을 획득한 1개의 스레드만 허용하기 떄문에 스레드가 매우 많을 경우 성능 저하 문제와 데드락이 발생할 위험이 있다.

### 2.2 CAS 연산

**CAS(Compare and swap)**은 락 없이 데이터를 안전하게 업데이트할 수 있는 lock-free 기법 중 하나이다.

CAS 연산은 (메모리 위치, 기존 값, 새로운 값)을 가지고 연산을 진행한다.

1. ‘현재 메모리 위치에 있는 값’과 ‘기존 값’을 비교한다.
2. ‘현재 메모리 값’과 ‘기존 값’이 같으면 ‘새로운 값’으로 바꾼다.
3. 만약 다르다면, 아무런 작업을 하지 않고, 값을 그대로 둔다.

이 작업은 원자적으로 수행되며 다른 스레드가 값을 변경하더라도 동시성 문제가 발생하지 않는다.

하지만 값을 변경하기 위해서 비교 연산이 필수이기 때문에 값 충돌이 잦다면 성능에 부담을 줄 수 있다.

자바에서는 Unsafe 클래스에서 위 연산을 지원한다.

```java
// <https://github.com/openjdk/jdk8/blob/master/jdk/src/share/classes/sun/misc/Unsafe.java>
// CAS 연산은 native 코드로 구현되었다.
public final native boolean compareAndSwapObject(Object o, long offset,
                                                     Object expected,
                                                     Object x);
```

## 3. Java 7, 8에서 ConcurrentHashMap의 차이

### 3.1 Java 7 ConcurrentHashMap

Java 7은 Segment를 여러 개로 나눠서, 각 Segment에 개별의 락을 걸어 접근이 가능하다. 이는 모니터 락의 동작 원리와 HashMap의 구현을 고려한 것이다.

만약 노드에 접근하기 위해서 1개의 lock만 허용하면 어떻게 될까? 많은 스레드가 map에 접근할 떄 락 획득까지 대기하는 시간 때문에 높은 성능을 기대할 수 없다.

![그림 2. Java 7 ConcurrentHashMap의 간단한 Segment 구조 그림](https://prod-files-secure.s3.us-west-2.amazonaws.com/3eb500f6-ee39-48be-bde3-905c2bc684b1/fa81e9e0-0b2a-4405-9a3d-36a45c3836b6/concurrenthashmap_segment2.drawio.png)

그림 2. Java 7 ConcurrentHashMap의 간단한 Segment 구조 그림

자바에서 HashMap은 해쉬 충돌이 발생 시 Separate Chaining 기법을 사용하기 때문에 **동시 접근하는 스레드들이 같은 Entry를 공유하지 않는다면 전체 락을 사용하는 것보다 ‘부분(Segment)’로 관리하는 것이 더 효율적이다.** 이처럼 **‘여러 개의 세분화된 락’**을 사용하는 방식을 **Lock stripping** 라고 한다.

다만, 위 구조에서도 문제가 있다. **segment를 얼마만큼 쪼개야 하는가?** 너무 많은 segment는 굳이 필요없는 lock을 만들어 메모리 자원을 낭비하게 된다. 또 너무 적은 segment는 위에서 언급한 1개의 락과 비슷한 효과를 가져올 수 있다. 적절한 segment를 나누는 것이 성능과 메모리 공간의 이점을 얻을 수 있다.

이것을 구현한 OpenJdk 7 ConcurrentHashMap은 아래와 같다.

```java
// <https://github.com/openjdk/jdk7u/blob/master/jdk/src/share/classes/java/util/concurrent/ConcurrentHashMap.java>
static final class Segment<K,V> extends ReentrantLock implements Serializable {

// ...

	// segment 당 별도의 table을 가지고 있다.
  transient volatile HashEntry<K,V>[] table;
  
// ...

	// segment에 데이터를 넣을 때는 lock을 획득해야 한다.
	final V put(K key, int hash, V value, boolean onlyIfAbsent) {
						// 락을 우선 획득한다.
            HashEntry<K,V> node = tryLock() ? null :
                scanAndLockForPut(key, hash, value);
            V oldValue;
            try {
                HashEntry<K,V>[] tab = table;
                int index = (tab.length - 1) & hash;
                HashEntry<K,V> first = entryAt(tab, index);
                for (HashEntry<K,V> e = first;;) {
                    if (e != null) {
                        K k;
                        if ((k = e.key) == key ||
                            (e.hash == hash && key.equals(k))) {
                            oldValue = e.value;
                            if (!onlyIfAbsent) {
                                e.value = value;
                                ++modCount;
                            }
                            break;
                        }
                        e = e.next;
                    }
                    else {
                        if (node != null)
                            node.setNext(first);
                        else
                            node = new HashEntry<K,V>(hash, key, value, first);
                        int c = count + 1;
                        if (c > threshold && tab.length < MAXIMUM_CAPACITY)
                            rehash(node);
                        else
                            setEntryAt(tab, index, node);
                        ++modCount;
                        count = c;
                        oldValue = null;
                        break;
                    }
                }
            } finally {
		            // 명시적인 락을 사용했기 때문에 반드시 lock을 해제해야 함.
                unlock();
            }
            return oldValue;
        }
        
 // ...
 
 }
```

```java
// <https://github.com/openjdk/jdk7u/blob/master/jdk/src/share/classes/java/util/concurrent/ConcurrentHashMap.java>
public class ConcurrentHashMap<K, V> extends AbstractMap<K, V>
        implements ConcurrentMap<K, V>, Serializable {
        
// ...

	// ConcurrentHashMap은 여러 개의 segments를 관리한다.
	final Segment<K,V>[] segments;

// ...

	// 새로운 값을 넣을 때는 hash 값을 구한 뒤, 해당 hash값을 가지는 segment(HashEntry array)에 값을 넣는다.
	public V put(K key, V value) {
        Segment<K,V> s;
        if (value == null)
            throw new NullPointerException();
        int hash = hash(key);
        int j = (hash >>> segmentShift) & segmentMask;
        if ((s = (Segment<K,V>)UNSAFE.getObject          // nonvolatile; recheck
             (segments, (j << SSHIFT) + SBASE)) == null) //  in ensureSegment
            s = ensureSegment(j);
        return s.put(key, hash, value, false);
    }
	
}
```

### 3.2 Java 8 ConcurrentHashMap

Java 8 이후에는 기존 Segment 방식에서 lock-free 기법인 CAS 방식으로 개선되었다. 때문에 Segment 구조로 여러 테이블 조각을 나눠서 관리하지 않고 단일 테이블 구조로 변경되었다.

```java
// <https://github.com/openjdk/jdk8/blob/master/jdk/src/share/classes/java/util/concurrent/ConcurrentHashMap.java>

// ...

// 단일 테이블 구조로 관리한다.
transient volatile Node<K,V>[] table;

// ...

 final V putVal(K key, V value, boolean onlyIfAbsent) {
        if (key == null || value == null) throw new NullPointerException();
        int hash = spread(key.hashCode());
        int binCount = 0;
        for (Node<K,V>[] tab = table;;) {
            Node<K,V> f; int n, i, fh;
            // 새로운 값을 넣을 때는 lock-free 기법을 사용한다.
            if (tab == null || (n = tab.length) == 0)
                tab = initTable();
            else if ((f = tabAt(tab, i = (n - 1) & hash)) == null) {
                if (casTabAt(tab, i, null,
                             new Node<K,V>(hash, key, value, null)))
                    break;                   // no lock when adding to empty bin
            }
            else if ((fh = f.hash) == MOVED)
                tab = helpTransfer(tab, f);
            // 이미 노드가 존재한다면 synchronized 블록 안에서 연산한다.
            else {
                V oldVal = null;
                synchronized (f) {
                    if (tabAt(tab, i) == f) {
	                    // 일반 노드로 관리 시 연산
                        if (fh >= 0) {
                            binCount = 1;
                            // 연결된 노드의 끝지점까지 반복 후, 새로운 노드를 추가
                            for (Node<K,V> e = f;; ++binCount) {
                                K ek;
                                if (e.hash == hash &&
                                    ((ek = e.key) == key ||
                                     (ek != null && key.equals(ek)))) {
                                    oldVal = e.val;
                                    if (!onlyIfAbsent)
                                        e.val = value;
                                    break;
                                }
                                Node<K,V> pred = e;
                                if ((e = e.next) == null) {
                                    pred.next = new Node<K,V>(hash, key,
                                                              value, null);
                                    break;
                                }
                            }
                        }
                        // Tree 구조로 관리 시 연산
                        else if (f instanceof TreeBin) {
                            Node<K,V> p;
                            binCount = 2;
                            if ((p = ((TreeBin<K,V>)f).putTreeVal(hash, key,
                                                           value)) != null) {
                                oldVal = p.val;
                                if (!onlyIfAbsent)
                                    p.val = value;
                            }
                        }
                    }
                }
                if (binCount != 0) {
		                // 임계값을 넘으면 해시 충돌시 seperate chaining으로 관리하던 것이 Tree로 관리된다.
                    if (binCount >= TREEIFY_THRESHOLD)
                        treeifyBin(tab, i);
                    if (oldVal != null)
                        return oldVal;
                    break;
                }
            }
        }
        addCount(1L, binCount);
        return null;
    }
    
// ...
```

위 코드에서 주목할 것이 **이미 존재하는 노드가 있다면(해시 충돌이 발생했다면), synchronized 블록을 사용해서 새로운 값을 추가한다.** 왜 CAS 연산이 아닌 lock 기법을 굳이 다시 사용하는 것일까?

Java의 HashMap은 해시 충돌이 발생할 경우, 연결 리스트 혹은 레드-블랙 트리 구조로 데이터를 저장한다. 이 때문에 새로운 값을 추가하기 위해서는 충돌이 발생한 해시 노드를 순회할 필요가 있다. 이 연산은 연결 리스트, 트리 구조에서 **‘순회 - 새로운 노드 추가’ 과정이 ‘원자적으로 수행’되어야 한다.**

CAS 연산은 단순 비교 후 값을 업데이트할 경우 적합하나 위 처럼 복잡한 연산의 원자성을 보장하기 위해서는 락 기법이 필요하다.

## 4. ConcurrentHashMap은 어디에 활용할 수 있을까?

ConcurrentHashMap은 멀티 스레드 환경에서 데이터 일관성을 지킬 필요가 있는 경우에 적합하다.

예를 들어 멀티 스레드 환경에서의 캐시, 웹 어플리케이션 세션 관리, 애플리케이션에서 공유 자원 관리 등 다양한 환경에서 사용할 수 있다.

### References
---
[https://github.com/openjdk/jdk7u/blob/master/jdk/src/share/classes/java/util/concurrent/ConcurrentHashMap.java](https://github.com/openjdk/jdk7u/blob/master/jdk/src/share/classes/java/util/concurrent/ConcurrentHashMap.java)
[https://github.com/openjdk/jdk8/blob/master/jdk/src/share/classes/java/util/concurrent/ConcurrentHashMap.java](https://github.com/openjdk/jdk8/blob/master/jdk/src/share/classes/java/util/concurrent/ConcurrentHashMap.java)
[https://en.wikipedia.org/wiki/Compare-and-swap](https://en.wikipedia.org/wiki/Compare-and-swap)
https://en.wikipedia.org/wiki/Monitor_(synchronization)#Implicit_condition_variable_monitors
