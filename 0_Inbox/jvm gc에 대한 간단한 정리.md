created at : 2024-10-24 12:55

#### tags

#

--- 

- jvm은 gc가 발생하면 메모리를 os에 release 하는가?
	- releases는 가능하다
		- gc 알고리즘 차이?
			- 알고리즘에 따라 release하지 않을 수도 있다.
		- gc 설정 차이?
			- maxheafreeratio, minheapfreeratio와 같이 설정으로 조절이 가능하다
	- 왜 항상 release하지 않는가?
		- release는 system call을 호출한다. 이는 비용이 높은 작업이다. -> gc 발생 시 stw 시간이 길어질 수 있다
			- 시스템 콜은 커널 모드로 진입하는데, 이 때 tlb, 캐시가 flush될 수 있다. 운영체제 명령어와 사용자 명령어는 메모리를 공유하지 않을 수 있기 때문이다.
			- 다른 커널 스레드가 메모리에 작업할 수 있기 때문에 메모리 할당 작업은 동기화가 필요하다.
			- 메모리를 할당한 뒤 여러 보안이나 권한을 위한 검증 작업도 필요하다.
		- release하는 작업은 비용이 크기 때문에 가능한 안 하는 것이 좋다.
	- 하지만 필요에 의해 Release할 수도 있다. 알고리즘 선택, 설정에 영향을 받는다.
- gc 알고리즘 기초
	- tri-color marking
- gc의 종류
	- serial
		- 단일 스레드 실행, mark-sweep-compact
		- 단순한 구현, 적은 메모리
		- stw 시간이 길다
	- parallel
		- 여러 스레드가 gc를 수행
		- 멀티코어 활용, 처리량이 높음
		- stw 여전히 발생한다.
	- cms
		- 초기 마킹에 stw 발생, 이후 애플리케이션 스레드와 병렬로 marking 실행, remark 시 stw 발생, 이후 애플리케이션 스레드도 동작하면서 sweep이 병렬로 진행
		- stw 시간을 최소화함.
		- compact이 없어 메모리 단편화 발생
	- g1
		- heap을 region으로 나눠서 수집
		- 예측 가능한 stw
		- 메모리 오버헤드 발생
	- zgc
		- 병렬 gc 실행
		- 동시 compact
		- 짧은 stw, 대용량 처리 적합
		- cpu 사용량이 높다
- 객체 할당 TLAB
	- thread local allocation buffers
		- 여러 스레드가 eden에 객체를 할당할 때, 메모리 영역이 겹칠 수도 있다.
		- lock으로 방지할 수 있으나 그냥 메모리 공간을 더 소비해서 속도를 얻는다.
		- 스레드마다 고유의 메모리 영역을 할당받아서 바로 해당 영역에 객체 할당이 가능하다.
	- tlab이 꽉차면?
		- 해당 스레드를 위한 새로운 tlab 생성 후 할당
		- tlab 영역 외의 eden에 할당
		- eden이 꽉 차있다면 gc 발생 후 할당
- gc가 compact하는 과정은 old에서만 일어나는가?
	- young은 필요없는 객체가 삭제되거나 s0,s1 영역으로 넘어가는데, 이 때 굳이 compact이라는 과정이 필요한가?
		- young에서는 compact이 필요없다.
			- eden이 꽉차면 s0 or s1으로 이동한다.
			- s0과 s1는 둘 중 하나만 사용하고 하나는 비어 있다.
			- eden이 다시 꽉차면 비어있는 s0 or s1으로 이동한다.
			- 이동할 때마다 정렬하면 파편화가 발생하지 않음.
			- minor gc라고도 한다
			- minor gc도 stw가 발생한다
	- old gc는 compact이 발생한다.
- gc는 young, old를 각각 따로 동작한다.
	- serialgc는 단일
	- 그 외는 여러 스레드(구체적으로 스레드 개수는 알고리즘마다 다른 듯, 설정할 수도 있음)
- minor, major, full
	- minor (eden, s0, s1)
	- major (old == tenured)
	- full (minor + major)
		- full == major일 수도 있다.
			- old generation에 의해 gc가 발생해도 young에서 gc 처리할 수도 있음
	- gc는 서로 연관되어 있다.
	- 중요한 것은 애플리케이션 thread가 멈추는 걸 최소화하는 것.
- gc는 young과 old를 다른 알고리즘을 적용할 수도 있었다
	- 요즘은 g1, zgc처럼 통합으로 관리하는 듯
### References
---
[]()
https://www.baeldung.com/gc-release-memory
https://en.wikipedia.org/wiki/Tracing_garbage_collection
https://docs.oracle.com/javase/8/docs/technotes/guides/vm/gctuning/sizing.html
https://developers.redhat.com/articles/2021/08/20/stages-and-levels-java-garbage-collection
https://dzone.com/articles/minor-gc-vs-major-gc-vs-full
https://stackoverflow.com/questions/16549066/java-major-and-minor-garbage-collections
https://www.baeldung.com/gc-release-memory
https://www.baeldung.com/java-jvm-tlab