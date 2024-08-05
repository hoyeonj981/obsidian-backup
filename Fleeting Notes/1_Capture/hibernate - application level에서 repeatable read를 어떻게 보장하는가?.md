created at : 2024-06-18 18:02

#### tags

#

--- 

- repeatable read
	- A 트랜잭션이 처리하던 row a가 B 트랜잭션이 row a를 중간에 변경해도 row a는 같은 결과를 바라보며 트랜잭션 처리가 가능하다.
- hibernate에서도 같은 결과가 발생할 수 있음
	- 이미 로드된 entity A, 변경 사항이 flush는 안 됨.
	- 다른 세션에서 entity A에 해당하는 row A를 변경해도 entity A는 변경된 row A가 아닌 entity A를 바라보며 작업이 처리된다.
	- 어찌보면 간단함
		- 메모리에 이미 엔티티가 로드된 상태라면 다시 조회해도 해당 인스턴스만 반환한다.
		- 최신 데이터를 조회하고 싶다면 refresh()를 사용해서 다시 조회한다.

### References
---
[]()
https://vladmihalcea.com/hibernate-application-level-repeatable-reads/