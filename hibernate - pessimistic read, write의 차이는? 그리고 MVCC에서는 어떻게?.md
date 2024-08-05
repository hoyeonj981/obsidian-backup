created at : 2024-07-01 18:08

#### tags

#

--- 

- pessimistic read
	- shared lock을 얻을 수 있음.
	- update, delete를 막는다.
	- dirty read가 발생하지 않는다면 적절한 선택
- pessimistic write
	- exclusive lock을 얻는다.
	- read, update, delete를 막는다.
	- 베타적인 접근이 필요할 경우 선택
	- select for update 쿼리가 나가는 것을 확인.
- pessimistic force increment
	- pessimistic write와 비슷하나 Version이 관리되는 entity에 version이 증가하게 됨.

- 최신 RDBMS는 lock 기반도 지원하지만 MVCC도 지원함.
	- jpa에서 pessimistic locking을 사용하더라도 db에서 mvcc 엔진을 사용할 경우 어떻게 동작?

- mvcc에서는 shared lock이 isolation level에 따라 다르게 acquired/released 된다.
	- 그렇다면 jpa에서 굳이 lock type을 정해서 전달할 필요가 있는가? 어차피 isolation level이 정해져 있는데?
	- 쿼리가 나갈 때 일정 부분만 isolation level을 지정되나?

- jpa은 기본적으로 read committed 레벨을 가정하고 동작함.
	- db가 이것보다 높은 고립 수준일 경우 어떻게?
	- jpa에서 따로 관리 db에서 따로 관리인가?
	- 일단 optimistic locking은 db가 아닌 jpa에서 관리가 된다.

- vlad mihalcea 블로그 설명
	- optimistic
		- entity의 상태에서 version을 통해 관리
	- low-level locking
		- 현재 동작하는 transaction isolation level을 기반
		- insert, update, delete 쿼리가 해당 row에 베타적인 락을 걸 수도 있다.
		- 일반적인 상황에서는 적합할 수 있으나, 동시성 제어 최적화에는 부적절할 수도 있다.
		- readers-writer locking
			- **Readers** - **Writer** 임. (읽기 여러개 쓰기 한개)
			- shared lock은 writers를 block, readers는 허용
			- exclusive lock은 writers, readers 모두 block
		- lock은 sql 표준에 명시가 안 됨. db마다 lock을 거는 방법이 모두 다르다.
			- 자바를 사용할 땐 위 개념이 가려진 채 단순히 pessimistic read, write만 사용
		- 

- postgresql으로 테스트
	- read write에 따라서 다른 쿼리가 나가는 것을 확인
	- h2의 문제였던 것으로 판별
### References
---
[]()
https://vladmihalcea.com/how-does-mvcc-multi-version-concurrency-control-work/
https://vladmihalcea.com/optimistic-vs-pessimistic-locking/
https://vladmihalcea.com/a-beginners-guide-to-database-locking-and-the-lost-update-phenomena/
https://vladmihalcea.com/hibernate-locking-patterns-how-do-pessimistic_read-and-pessimistic_write-work/