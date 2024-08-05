created at : 2024-01-30 18:38

#### tags

#CS #Processor 

--- 

- mutual exclustion을 구현하는 방법은 다양하다.
	- 하드웨어적 방법
		- 간단하게 하나의 프로세서가 동작하는 동안 인터럽트를 금지하기
		- test and set (busy-waiting)
			- 이 방법은 하드웨어적으로 지원하는 경우도 있고 어셈블리 코드로 구현하는 방법도 있다.
	- 소프트웨어적 방법
- monitor lock은 mutual exclusion을 구현하는 방법들 중 하나이다.
- monitor lock을 구현이 하드웨어가 지원하면 하드웨어 기능을 사용하고 아닐 경우 어셈블리로 코드를 작성해서 제어할 수 있다.
- 그렇다면 cache flushing은 언제 발생하는가?
	- volatile은 모든 스레드가 같은 값을 봐야 한다. 그렇다면 캐시 일관성 규칙에 따라 플러시가 발생할 수도 아니면 필요한 캐시에만 데이터가 전달 될 수도 있다.

### References
---
[how are mutex and lock structures implemented](https://stackoverflow.com/questions/1726702/how-are-mutex-and-lock-structures-implemented)
[mutual exclusion](https://en.wikipedia.org/wiki/Mutual_exclusion)
