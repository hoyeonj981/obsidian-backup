created at : 2024-09-22 22:07

#### tags

#

--- 

## 1. 서론

ConcurrentHashMap의 동작 원리를 학습하던 중 Lock Stripping에 대한 키워드를 발견했다. Lock Stripping은 어떤 원리일까? 그리고 Java 8 이후에 바뀐 ConcurrentHashMap은 어떤 원리가 적용되었을까?

## 2. ConcurrentHashMap에 적용된 원리들

ConcurrentHashMap은 멀티 스레드 환경에서 안전하게 사용할 수 있는 HashMap이다. HashMap은 멀티 스레드 환경에서 안전성을 보장하지 않기 때문에 여러 스레드가 읽기-쓰기 작업을 할 경우 문제가 발생할 수 있다. ConcurrentHashMap에는 어떤 원리가 적용되었을까?

### 2.1 Synchronized 키워드

자바의 synchronized 키워드는 모니터(Monitor)로 동작한다.

모니터란 공유자원에 접근할 때 오직 하나의 스레드만 접근할 수 있고, 나머지 스레드는 진입 큐, 대기 큐에서 대기 상태에 놓인다. 이 때 조건 변수가 만족되기 전까지는 다른 스레드들은 대기 상태에 존재하고, 다른 스레드가 조건을 만족할 때 다른 스레드를 깨워서 공유 자원에 접근할 수 있다.

자바의 모든 객체는 synchronized 모니터로 동작하며, synchronized 메서드를 사용해 특정 메서드에만 모니터 락을 걸 수 있고 synchronzied 블록을 사용해서 특정 블록에만 모니터 락을 걸 수도 있다.

(위키피디아 사진)

하지만 공유 자원에는 오직 락을 획득한 1개의 스레드만 허용하기 떄문에 스레드가 매우 많을 경우 성능 저하 문제와 데드락이 발생할 위험이 있다.

### 2.2 CAS 연산

CAS(Compare and swap)은 락 없이 데이터를 안전하게 업데이트할 수 있는 lock-free 기법 중 하나이다.

CAS 연산은 (메모리 위치, 기존 값, 새로운 값)을 가지고 연산을 진행한다.

1. ‘현재 메모리 위치에 있는 값’과 ‘기존 값’을 비교한다.
2. ‘현재 메모리 값’과 ‘기존 값’이 같으면 ‘새로운 값’으로 바꾼다.
3. 만약 다르다면, 아무런 작업을 하지 않고, 값을 그대로 둔다.

이 작업은 원자적으로 수행되며 다른 스레드가 값을 변경하더라도 동시성 문제가 발생하지 않는다.

하지만 값을 변경하기 위해서 비교 연산이 필수이기 때문에 값 충돌이 잦다면 성능에 부담을 줄 수가 있다.

자바에서는 Unsafe 클래스에서 위 연산을 지원한다.

## 3. Java 7, 8에서 ConcurrentHashMap의 차이

### 3.1 Java 7 ConcurrentHashMap

Java 7은 Segment를 여러 개로 나눠서, 각 Segment에 개별의 락을 걸어 접근이 가능하다. 이는 모니터 락의 동작 원리와 HashMap의 구현을 고려한 것이다.

만약 모든 노드에 접근할 때 lock이 필요하다면 어떻게 될까? write 작업(put 연산)을 할 경우에는 lock이 필요하나 단순히 read 작업(get 연산)을 위해서는 굳이 사용할 필요가 없을 것이다.

또한, 자바에서 HashMap은 해쉬 충돌이 발생 시 Separate Chaining 기법을 사용하기 때문에 동시 접근하는 스레드들이 같은 Entry를 공유하지 않는다면 전체 락을 사용하는 것보다 ‘부분(Segment)’로 관리하는 것이 더 효율적이다.

이처럼 ‘여러 개의 세분화된 락’을 사용하는 방식을 **Lock stripping**라고 한다.

이것을 구현한 OpenJdk 7은 아래와 같다.

```java
// <https://github.com/openjdk/jdk7u/blob/master/jdk/src/share/classes/java/util/concurrent/ConcurrentHashMap.java#L350>
static final class Segment<K,V> extends ReentrantLock implements Serializable {

// ...

	// segment 당 별도의 table을 가지고 있다.
  transient volatile HashEntry<K,V>[] table;
  
// ...

	// segment에 데이터를 넣을 때는 lock을 획득해야 한다.
	final V put(K key, int hash, V value, boolean onlyIfAbsent) {
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
                unlock();
            }
            return oldValue;
        }
        
 // ...
 
 }
```

```java
// <https://github.com/openjdk/jdk7u/blob/master/jdk/src/share/classes/java/util/concurrent/ConcurrentHashMap.java#L103>
public class ConcurrentHashMap<K, V> extends AbstractMap<K, V>
        implements ConcurrentMap<K, V>, Serializable {
        
// ...

	// ConcurrentHashMap은 여러 개의 segments를 관리한다.
	final Segment<K,V>[] segments;

// ...

	// 새로운 값을 넣을 때는 hash 값을 구한 뒤, 해당 hash값을 가지는 segment에 값을 넣는다.
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


### References
---
[]()
[https://github.com/openjdk/jdk7u/blob/master/jdk/src/share/classes/java/util/concurrent/ConcurrentHashMap.java](https://github.com/openjdk/jdk7u/blob/master/jdk/src/share/classes/java/util/concurrent/ConcurrentHashMap.java)

[https://github.com/openjdk/jdk8/blob/master/jdk/src/share/classes/java/util/concurrent/ConcurrentHashMap.java](https://github.com/openjdk/jdk8/blob/master/jdk/src/share/classes/java/util/concurrent/ConcurrentHashMap.java)