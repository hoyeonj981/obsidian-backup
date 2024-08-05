created at : 2024-07-08 00:20

#### tags

#

--- 

- @BatchSize
	- 초기화가 안 된 entity의 프록시를 fetching에 사용할 수 있음.
	- batch size에 명시된 크기만큼 로딩한다.
	- fetch join과 같이 n+1 문제를 해결할 수 있는 방법이다.
	- 단, 최적화를 위해서는 좋은 방법이 아니다.
		- n+1은 방지할 수 있으나, fetch join, dto projection이 더 효율적이다.
	- 나가는 쿼리 예시
	```
		select ... from ... where id in (여기에 배치 사이즈 만큼의 id들이 포함된다.)
	```

	- 
### References
---
[]()
https://docs.jboss.org/hibernate/orm/6.5/userguide/html_single/Hibernate_User_Guide.html#fetching-batchk