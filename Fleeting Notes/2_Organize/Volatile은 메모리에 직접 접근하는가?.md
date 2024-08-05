created at : 2024-01-25 16:25

#### tags

#Java #MemoryModel #CS #Cache

--- 

- 캐시 일관성 (다른 메모로 남김)
- 메모리에 직접 접근하는 방법은 매우 비효율적
	- 효율적인 연산을 위해서 프로세서 내부 캐시에 메모리 일부분을 퍼 와서 연산함
- 굳이 write 작업할 때마다 계속 메모리에 직접 접근할 필요가 있나?
	- 일관성만 지킬 수 있다면 굳이 접근할 필요는 없다
- 캐시 일관성은 미스가 발생하면 단순히 메모리에 접근하는 것이 아닌 계층구조와 다른 캐시들 간의 공유 상태등 다양한 경우의 수가 존재한다.
	- 복잡하지만 메모리에 접근하지 않고 일관성을 지킬 수는 있다.
- 하드웨어 수준에서 일관성을 보장하는데, 왜 소프트웨어 수준에서 일관성을 보장하는 키워드(volatile)이 존재하는가?
	- JSR-133 설명
		- 프로그래머가 volatile이라고 선언한 변수는 스레드가 'stale'한 변수를 보지 않는다는 것을 의미
			- 이것은 단순히 write된 변수만 본다는 것이 아닌 명령어가 reordering 되어도 항상 동일하다는 것을 보장한다.

- JSR-133 이전 volatile
	- 과거에도 reordering을 금지했다.
	- 하지만 volatile과 non-volatile이 섞여서 쓰일 때, reordering이 될 수 있었다.
	- JSR-133 이후에는 volatile과 함께 쓰이는 non-volatile도 reordering이 금지된다.
		```
		class VolatileExample {
			  int x = 0;
			  volatile boolean v = false;
			  
			  public void writer() {
			    x = 42;
			    v = true;
			}

		  public void reader() {
		    if (v == true) {
		      //uses x - guaranteed to see 42.
		    }
		  }
		}
		```
	- 과거 모델에서는 x가 0일 수 있었다. 그러나 현재는 42를 보장한다.
		- double checked locking before java5 (다른 메모로 남김)

- volatile은 해당 변수만 volatile이다. 객체 내부나 배열의 원소까지 모두 volatile인 것은 보장하지 않는다.

- 결론
	- volatile은 다른 스레드들도 같은 데이터를 볼 수 있도록 보장한다.
		- visibility를 보장한다.
	- 이것은 반드시 메모리 직접 접근을 야기하는 것이 아니다
	- 일단 캐시를 찾아보고 캐시 일관성 프로토콜대로 동작하여 다른 프로세서의 캐시를 업데이트한다.
	- 그래도 메모리에 접근이 필요하다면 접근하는 것이다.
### References
---
[Myths programmers believe about cpu caches](https://software.rajivprab.com/2018/04/29/myths-programmers-believe-about-cpu-caches/)
[Is volatile expensive? - stackoverflow](https://stackoverflow.com/questions/4633866/is-volatile-expensive)
[Java Concurrency : understanding volatile keyword](https://dzone.com/articles/java-concurrency-understanding-the-volatile-keyword)
[다른 언어에서 volatile](https://johngrib.github.io/wiki/java/volatile/)
[comparing the volatile keyword. in java c, c++ - blog](https://componenthouse.com/2016/12/28/comparing-the-volatile-keyword-in-java-c-and-cpp/)
[happens-before](https://javarevisited.blogspot.com/2020/01/what-is-happens-before-in-java-concurrency.html)
