created at : 2024-01-30 17:10

#### tags

#Java #MemoryModel #CS 

--- 

- volatile은 happens-before, visibility를 보장한다.
- 그렇다면 아래와 같은 코드에서 왜 문제가 발생하는 것일까
```
public class p1 {  
  
    static volatile int count = 0;  
  
    public static void main(String[] argv) {  
  
        IntStream.range(0, 1000)  
                 .parallel()  
                 .forEach(e -> count++);  
  
        System.out.println(count);  
  
    }  
}

```

- 결과는 항상 동일하지 않다.
	- 다른 스레드가 같은 값을 바라볼 수 있다면, 굳이 locking 알고리즘을 사용한 원자적 연산을 할 필요가 없다.
	- 근데 문제가 P생긴다. 원자적 연산이 안 된다는 뜻이다.
	- 왜 그런가?
- 원자적 연산으로 값을 업데이트하는 것은 이해가 된다.
- 문제가 되는 동작은 read-update-write
	- 모든 스레드가 같은 값을 바라 보는데 문제가 왜 생기는가?
	- 같은 값은 바라볼 수 있는데 업데이트가 순차적으로 진행되는 것을 보장하지 않기 때문?
- volatile happens before를 통해서 동기화 기법으로 활용이 가능하다. 
	- 라이브러리 성능을 위해서 그렇게 구현한 경우가 존재 (synchronization piggyback)
		- 하지만 일관성이 깨지기 쉽다는 문제가 있다. 동기화 기법을 최적화할 정도로 성능에 민감한 경우 사용한다.
	- happens before는 말하자면 어셈블리 기법을 통한 동기화
	- JMM은 최소한 스레드 A가 쓴 최신 값을 스레드 B가 볼수 있도록 보장해준다. 하지만 그 후에 쓰는 내용은 볼 수도 있고 보지 못할 수도 있다. (JICP - p.499)
- 결론
	- happens before를 통한 동기화를 만들 수가 있다.
		- synchronization piggyback
			- FutureTask 내부 클래스 Sync (JICP - p.495)
			- 그러나 이 방법은 매우 민감하여 필요한 경우가 아니라면 그냥 일반적인 방법이 더욱 좋다.
	- 왜 volatile의 happens before를 통해서 atomic operation을 보장할 수 없는가?
		- JMM은 최소한 스레드 A가 쓴 최신 값을 스레드 B가 볼수 있도록 보장해준다. 하지만 그 후에 쓰는 내용은 볼 수도 있고 보지 못할 수도 있다. (JICP - p.499)
		- 즉, read-update-write 작업에서 read할 땐 최신인데, update-write한 작업이 순차적일 수도 있고 덮어 씌울 수도 있기 때문에 atomic한 동작을 지킬 수 없다.
### References
---
[]()
