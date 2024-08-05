created at : 2024-02-12 14:01

#### tags

#CS #Processor #MemoryModel 

--- 

- consistency
	- 일관된 규칙, 모델 등을 설명하는 모델을 뜻한다.
- coherence
	- 데이터의 일관성을 뜻한다.
- 메모리 모델인 무엇인가? (좁은 의미)
	- 하드웨어와 소프트웨어 사이의 계약.
	- 하드웨어가 채택한 모델 안에서 연산 순서의 변경이 허용된다.
	- 넓은 의미
		- 분산 시스템에서 데이터를 공유할 때의 일관성도 포함된다. (파일 시스템, 데이터베이스, 복제, 웹 캐싱 등)
- sequential consistency
	- 병렬 프로그래밍을 작성할 때 순서대로 진행된다
		- 프로그램 순서가 코드가 작성된 순서대로 동작한다. (reordering이 없음)
		- 단, 이것은 컨텍스트 스위칭으로 작업 순서가 변경되는 것을 막는다는 뜻이 아니다.
			```
				--Thread 1      ---Thread 2
				A = 1           B = 2
				pring(B)        print(A)
			```
		- 위 연산은 00, 01, 10으로 나타날 수도 있다.
	- 한 명령어만 수행하기 때문에 매우 느리다.
		- 명령어 재배치로 메모리에서 데이터를 가져오는 연산을 뒤에 미루고 일단 계산을 우선하게 바꿀 수가 있다. -> cpu 사이클을 아낄 수 있다.
		- 순서대로 진행할 경우 낭비되는 사이클이 있을 수 있다. -> 느리다.
			- 다른 스레드도 똑같이 볼 수 있어야 하기 때문에 write 작업은 느리다.
- relaxed memory model
- Total Store Ordering
	- write 작업 시 바로 메모리(공유 메모리)에 반영하는 것이 아닌 write buffer에 일단 저장한다.
		- write buffer는 프로세서 안에 존재
	- read 작업은 write 작업이 끝난 즉시 실행이 가능하다. (사이클 낭비가 적다)
	- 이후 write한 작업이 반영할 때 캐시로 가져와서 다른 스레드가 볼 수 있도록 반영한다.
	```
		---Thread 1
		A = 1
		print(A)
	```
	- 위와 같은 경우 write A = 1 작업이 캐시에 없고 buffer에만 존재함.
		- print A 작업 시 공유 메모리에서 데이터를 가져올 때 old value를 가져올 수 있다.
		- 하지만 같은 코어에서 작업할 경우 store buffer에서 읽기 때문에 최신 데이터를 읽을 수 있다.
	- TSO는 거의 sequential consistency를 보장한다.
	- 하지만 TSO는 허용하나 SC가 허용하지 않는 것이 존재.
		- TSO 하드웨어에서는 프로그래머가 예상하지 못한 일이 발생할 수 있다.
	```
		--Thread 1      ---Thread 2
		A = 1           B = 2
		pring(B)        print(A)
		
	```
	- A와 B는 각각 store buffer에 쓰여진 상태이고 main memory가 각각 0인 상태라면 write 연산과 다른 결과를 출력한다.
		- SC에 위배되는 결과.
	- 현대 대부분 아키텍처는 store buffer를 가지고 있으며, 최소한 약한 TSO를 구현했다.
- x86과 arm
	- x86은 거의 순차적인 일관성을 지킨다.
	- arm reordering을 자유롭게 가능하여 최적화된 실행이 가능하다. 그만큼 프로그래머가 예측하기 힘든 결과를 얻을 수 있다.
	- 대부분 현대 아키텍처는 relaxed model을 사용하는데 이 때 일어나는 최적화 작업을 제어하기 위한 연산을 지원 (memory barrier or fence)
	- 
### References
---
[메모리 일관성 모델 - blog](https://hyeyoo.com/170)
[memory barriers: a hardware view for software hackers](https://www.puppetmastertrading.com/images/hwViewForSwHackers.pdf)
[memory consistency models : a tutorial](https://www.cs.utexas.edu/~bornholt/post/memory-models.html)
[CPU memory model](https://bajamircea.github.io/coding/cpp/2019/10/25/cpu-memory-model.html)


