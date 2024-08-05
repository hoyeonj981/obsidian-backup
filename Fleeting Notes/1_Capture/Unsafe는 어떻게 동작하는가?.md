created at : 2024-01-30 14:33

#### tags

#Java #MemoryModel #CS 

--- 

- unsafe는 무엇인가?
	- java에서 low-level operation을 지원하는 클래스
		- cpu, 하드웨어에 직접 접근
		- 생성자 없이 객체 생성
		- off-heap 메모리 사용 (가비지 컬렉션이 관리하지 않음)
		- 그 외 등
- 왜 이런것이 존재하는가?
	- vm을 통해서 하드웨어 독립적인 코드를 사용할 수 있으나, 성능을 위해서는 하드웨어에 종속적인 코드를 작성할 필요가 있다.
	- 예시
		- synchronized와 atomic class 성능 차이는 2~3배 정도
		- 왜?
			- lock free 알고리즘은 하드웨어 종속적인 연산이 필요하다.
- 주로 어디에 사용되고 있는가?
	- 대표적인 사용 예시는 atomic class
	- 많은 프레임 워크에서 성능을 위해서 사용하고 있다.
- 현재 상황은?
	- 자바는 플랫폼 독립적인데 그러기 위해서 성능을 포기할 것인가?
	- varhandle과 같은 대안으로 점차 대체하고 있다.
- varhandle이란 무엇인가
	- (다른 메모로 남김)
- cas algorithm (다른 메모로 남김)


### References
---
[the unsafe class : unsafe at any speed](https://blogs.oracle.com/javamagazine/post/the-unsafe-class-unsafe-at-any-speed)
[what's the difference between compareAndSet and weakCompareAndSet in AtomicReference?](https://stackoverflow.com/questions/36428044/whats-the-difference-between-compareandset-and-weakcompareandset-in-atomicrefer)
