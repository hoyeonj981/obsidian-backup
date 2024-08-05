created at : 2024-05-12 14:44

#### tags

#

--- 

- 일반적으로 table보다 클래스가 많아야 한다고 함.
- 객체지향에서는 상속을 통해서 확장할 수 있다.
	- 권장방법은 composition이긴 함.
- 하지만 상속을 통해서 확장을 하면 dbms와 oop 간의 불일치가 발생
	- 이를 보완하는 방법이 orm에서 지원한다.
- 그렇다면 상속을 통한 확장을 할 때,
	- 새로운 속성을 현재 기존 테이블에서 추가할 것인가?
		- nullable인 경우, 괜찮다.
	- 새로운 테이블을 새로 생성할 것인가?
		- 만약 제약조건으로 not null을 넣을 경우, 새로운 테이블로 데이터를 옮기는 과정이 필요하다.
	- 위 둘의 비용적 차이는 얼마인가?

### References
---
[]()
https://stackoverflow.com/questions/32601041/sql-server-cost-of-adding-a-column-to-an-existing-table