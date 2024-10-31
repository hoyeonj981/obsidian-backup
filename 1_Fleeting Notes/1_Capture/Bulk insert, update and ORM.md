created at : 2024-04-27 02:01

#### tags

#

--- 

- DBMS에서 bulk insert, update를 어떻게 사용?
	- 사용하면 어떤 장점?
- ORM에서 말하는 bulk 기능은 DBMS에 맞춰서 적절한 쿼리를 날리는 기능인가?
	- 아니면 entity를 관리하는 메모리 효율을 위한 것인가?
		- 메모리에 접근하는 것보다 메모리에 올라오는 엔티티 수가 더 중요하지 않을까?
- bulk 기능이 정확히 어떤 기능이고 어떤 목적인지 구별할 필요가 있다고 생각
- 실제로 돌려보면 어떤 차이가 있는지 눈으로도 확인하고 싶다.

- bulk, non-bulk 왜 구분? 어떤 차이 때문에?ㅖ
	- 책에서는 생명주기를 제어하는 콜백 메서드가 실행됨 - non bulk
	- bulk는 생명주기를 제어하는 콜백 메서드가 실행이 안 된다고 함.
	- 이게 무슨 말? 왜 생명주기 관리?

### References
---
[]()
https://www.mssqltips.com/sqlservertip/5636/optimize-large-sql-server-insert-update-and-delete-processes-by-using-batches/
https://dev.mysql.com/doc/refman/8.0/en/insert-optimization.html
https://stackoverflow.com/questions/50772230/how-to-do-bulk-multi-row-inserts-with-jparepository
