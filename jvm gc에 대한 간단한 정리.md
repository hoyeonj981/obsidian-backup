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
- gc 알고리즘 기초
	- tri-color marking
- gc의 종류
	- serial
	- parallel
	- cms
	- g1
	- 
- 객체 할당 TLAB
	-
- gc가 compact하는 과정은 old에서만 일어나는가?
	- young은 필요없는 객체가 삭제되거나 s0,s1 영역으로 넘어가는데, 이 때 굳이 compact이라는 과정이 필요한가?
### References
---
[]()
https://www.baeldung.com/gc-release-memory
https://en.wikipedia.org/wiki/Tracing_garbage_collection
https://docs.oracle.com/javase/8/docs/technotes/guides/vm/gctuning/sizing.html