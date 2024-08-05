created at : 2024-01-25 16:22

#### tags

#Java #CS #DesignPattern #MemoryModel #Lock

--- 

```
private static Something instance = null;

public Something getInstance() {
  if (instance == null) {
    synchronized (this) {
      if (instance == null)
        instance = new Something();
    }
  }
  return instance;
}
```
- before jsr-133
	- 바이트코드를 까보면 생성자도 메서드로 호출하는 과정이 존재한다.
	- JIT에 의해서 init() 호출해서 생성하는 코드와 write하는 코드의 순서가 바뀔 수가 있었다.
- java 5 jmm에서는 이 문제가 개선이 되었다.
	- volatile 키워드는 reordering을 금지하고 읽기작업은 쓰기 작업이 이후에 발생하는 것을 보장한다.
```
Object obj = new Object();

0: new #2 // class java/lang/Object 
3: dup 
4: invokespecial #1 // Method java/lang/Object."<init>":()V 
7: astore_1
```

### References
---
[Double checked locking is broken](https://www.cs.umd.edu/~pugh/java/memoryModel/DoubleCheckedLocking.html)
[jsr-133#dcl](https://www.cs.umd.edu/~pugh/java/memoryModel/jsr-133-faq.html#dcl)
[infoworld - dcl](https://www.infoworld.com/article/2074979/double-checked-locking--clever--but-broken.html)