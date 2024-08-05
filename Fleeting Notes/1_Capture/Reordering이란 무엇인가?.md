created at : 2024-01-25 16:04

#### tags

#cs #compiler #JIT #Processor 

--- 

- Reordering이란 무엇인가?
	- compile time reordering
		- high level - 프로그램 코드 순서
		- low level - 병렬 프로그래밍에서 서로 다른 스레드끼리 바라볼 때
	- runtime reordering
		- 아키텍처마다 채택한 메모리 일관성 모델이 다르다
			- 왜?
				- cisc는 메모리 사용을 최소화하기 위해서 설계됨. 명령어의 길이가 가변적(상수값을 유연하게 사용할 수 있다)
				- 명령어가 다양한 의미를 포함할 수 있다
					- 복잡한 디코더가 필요하다.
					- 컴파일러가 명령어를 최적화할 여지가 적음
				- risc는 복잡한 가변 명령어를 단순한 고정 명령어로 사용하기 위해서 만듦
					- 디코더가 단순하다.
					- 복잡한 해석이 적어 컴파일러 최적화의 여지가 있음.
			- 순차적 일관성
				- 모든 명령어가 순서대로 실행됨.
			- 느슨한 일관성
				- 경우에 따라 허용되는 reordering이 존재
			- 약한 일관성
				- 명시적인 메모리 펜스가 없다면 reordering은 임의로 발생함.
- 프로세서
- 컴파일러
- JIT (유사한 가상머신 수준에서)

### References
[memory ordering - wiki](https://en.wikipedia.org/wiki/Memory_ordering#cite_note-7)
[strong and weak hardware memory models](https://herbsutter.com/2012/08/02/strong-and-weak-hardware-memory-models/)
[memory model whitepaper on arm architecture](https://community.arm.com/arm-community-blogs/b/infrastructure-solutions-blog/posts/synchronization-overview-and-case-study-on-arm-architecture-563085493)
[sc-tso - pdf](https://www.cis.upenn.edu/~devietti/classes/cis601-spring2016/sc_tso.pdf)
[비순차 프로세서 파이프라인1 - blog](http://cloudrain21.com/out-of-order-processor-pipeline-1)
[비순차 프로세서 파이프라인2 - blog](http://cloudrain21.com/out-of-order-processor-pipeline-2)