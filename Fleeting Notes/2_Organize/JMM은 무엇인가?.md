created at : 2024-01-25 15:51

#### tags

#Java #CS #MemoryModel 

--- 

- Java Memory model은 무엇인가?
	- 자바에서 스레드들이 메모리와 상호작용을 할 때, 어떻게 할 것인지 설명하는 모델
	- JVM이 해야만 하는 최소환의 보장
	- 프로세서 간에 서로 메모리 내용을 공유할 때 조율하는 특별한 명령어 (메모리 베리어)가 있는데, 원래 프로그래머가 직접 조절할 수 있으나 자바는 JMM을 정의해서 JVM이 해당 아키텍처에 따라 적절히 알아서 조절한다.
	- JSR-133 이전에는 불완전했으나 이후 개선됨
- 왜 이런 모델이 필요한가?
	- 프로세서마다 다른 메모리 모델을 사용한다.
	- 프로세서마다 다른 캐시 일관성을 지원한다.
	- x86은 strong memory model, arm은 weak memory model을 사용하는 대표적인 예
	- thread 끼리 동작할 때 서로의 동작을 볼 수 없다.
	- 현대 프로세서들은 대부분 멀티 프로세서이기 때문에 효율적인 실행을 위해서 비순차적인 실행이 이루어진다. 자바는 다양한 플랫폼에서 동작할 수 있어야 하기 때문에 이러한 규칙을 정해서 일관성을 보장해야할 필요성이 있다.
- 강한 메모리 모델, 약한 메모리 모델은 어떠한 차이가 있는가?
	- 강한 메모리 모델 (다른 메모로 남김)
	- 약한 메모리 모델 (다른 메모로 남김)
- reordering이란 무엇인가?
	- reordering (다른 메모로 남김)
- happens-before relationship이란?
	- 작업 A, B 둘 사이에 하나의 작업은 항상 다른 작업보다 먼저 발생해야 하는 관계
	- 이 관계는 최적화를 위해 비순차적인 실행이 진행되어도 보장이 된다.
	- 즉, 최적화 때문에 happens-before 관계가 깨지지 않는다.
	- 만약 이러한 관계가 없다면 data race를 겪게 된다.
	- happens-before를 따르는 키워드
		- program order 규칙
			- 특정 스레드를 놓고 봤을 때 프로그램된 순서에서 앞서 있는 작업은 동일 스레드에서 뒤에 실행되도록 프로그램된 작업보다 미리 발생
				- 동일 스레드에서는 코드 순서대로 동작한다.
		- 모니터 잠금 규칙
			- 특정 모니터 잠금 작업 뒤에 오는 모든 모니터 잠금 작업보다 미리 발생
				- 먼저 락을 얻은 작업이 항상 먼저 실행됨.
		- volatile 변수 규칙
			- volatile 변수에 대한 쓰기 작업은 따라오는 해당 변수에 대한 모든 읽기 작업보다 먼저 발생
				- 항상 쓰기 작업 후 읽을 수 있다.
		- 스레드 시작 규칙
			- 특정 스레드에 대한 thread.start 작업은 시작된 스레드가 같고 있는 모든 작업보다 미리 발생
				- main thread에서 start 작업을 진행할 때, main thread가 먼저 동작하지 안에 있는 thread.start가 먼저 동작할 수는 없다.
		- 스레드 완료 규칙
			- 스레드 내부의 모든 작업은 다른 스레드에서 해당 스레드가 완료됐다는 점을 파악하는 시점보다 미리 발생. 특정 스레드가 완료되었는지 판단하는 방법 :  join, isAlive 메소드가 false를 리턴하는 것.
				- 스레드 내부 작업이 다 안 끝났는데, join 메서드가 false를 리턴할 수 없다.
		- 인터럽트 규칙
			- 다른 스레드를 대상으로 interrupt 메서드를 호출하는 작업은 인터럽트 당한 스레드에서 당했다는 사실을 파악하는 일보다 미리 발생. 사실을 파악하는 방법 : interrupt exception을 받거나 isInterrupted가 호출
				- 예외나 isInterrupted가 interrupt 메서드 호출보다 먼저 발생할 수 없다
		- finalizer 규칙
			- 특정 객체에 대한 생성 메서드가 완료되는 시점은 finalizer보다 먼저 실행
				- 생성도 안 된 것을 finalize할 수 없음
		- transitivity
			- A가 B보다 미리 발생하고, B가 C보다 미리 발생한다면 A는 C보다 먼저 발생해야 함.
				- 일반적인 규칙
- volatile은 어떻게 동작하는가?
	- volatile은 메모리에 직접 접근한다고 한다. 과연 그럴까? (다른 메모로 남김)
- final은 어떻게 동작하는가?
- synchronized는 어떻게 동작하는가?
	- synchronized lock (다른 메모로 남김)
- double checked locking에는 어떤 문제가 존재했는가?
	- double checked locking before java 5 (다른 메모로 남김)
- 저레벨 최적화
	- 프로세서 수준
		- 슈퍼스칼라
		- 파이프라이닝
		- 명령어 스케줄링
		- 비순차적 실행
		- 다중 메모리 캐시
	- 프로그램 수준
		- 컴파일러 최적화
		- 전역 레지스터 할당

### References
---
[JSR-133 FAQ 번역](https://medium.com/@qwefgh90/jsr-133-java-memory-model-faq-%EB%B2%88%EC%97%AD-128487aebc1e)
[Java Memory model - wiki](https://en.wikipedia.org/wiki/Java_memory_model)
[JLS-17.Thread and locks](https://docs.oracle.com/javase/specs/jls/se8/html/jls-17.html#jls-17.4.5)
[Java Memory Model Explained - youtube](https://www.youtube.com/watch?v=qADk_tj4wY8)
[Happens-before - wiki](https://en.wikipedia.org/wiki/Happened-before)
java in concurrency in practice - p.492