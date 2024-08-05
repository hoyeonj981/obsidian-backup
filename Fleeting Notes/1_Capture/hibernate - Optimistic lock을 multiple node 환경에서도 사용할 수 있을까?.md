created at : 2024-06-25 16:37

#### tags

#

--- 

- Optimistic lock은 Version을 통해서 관리한다.
- Version은 정수, 부동소수로 표현할 수 있음
- Update가 발생할 때 version 컬럼이 알고 있는 값과 같은지 확인한다.
	- 만약 update ~ where id = ?, version = ?
	- version이 다를 경우 예외가 발생함.
- 궁금증
	- 여러 노드에서도 Optimistic lock을 신뢰하여 사용할 수 있을까?
	- update 쿼리를 통해서 version을 확인하기 때문에 괜찮을 것 같기도 함.
- 궁금증 2
	- 만약 jpa, mybatis, jdbc 등 이렇게 서로 다른 기술을 사용할 경우는?
	- db에 의존하는 lock 기법을 사용하는 것이 좋을 듯?

### References
---
[]()
