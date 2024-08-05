created at : 2024-05-22 22:45

#### tags

#

--- 

- 외래키를 소유하는 쪽이 주인 엔티티라고 한다.
	- 왜?
		- 데이터베이스에서는 bidirectional relation이 없다. 한 쪽으로만 관계가 설정됨. (외래키를 통해서)
		- 외래키를 소유하는 쪽이 owning side다.
- 간단하게 mapped by가 없는 쪽이 주인임.
	- 그렇다면 주인으로 설정된 곳에는 create table 쿼리가 나갈 때 항상 외래키 컬럼과 제약 조건이 함께 나가야 하는 것이 아닌가?
		- One to many, many to one으로 설정했을 때 Many To One이 주인이다.
			- 즉, many to one 엔티티가 외래키를 가진다.
	- 그렇다면 uni directional인 경우 주인은 없는 것일까?
		- one to one인 경우
			- bidirectional
				- mappedby가 있다면 없는 쪽에 외래키가 설정됨
				- mappedby가 없다면 둘 다 외래키를 가진다.
			- unidirectional
				- one to one 애노테이션이 설정된 쪽에서 외래키를 가진다.
		- one to many인 경우
			- 항상 many 쪽이 외래키를 가진다
				- 왜?
					- 유저 : post (1 : n) 관계
						- 유저의 key 값을 post의 외래키로 가질 수 있다.
					- 유저가 post의 키 값을 가지는 관계로 만들 수 없는가?
						- 가능은 하나 정규화 및 데이터 무결성에 위배
						- many 쪽에서 one을 조회할 때 불필요한 중복 데이터가 쌓이게 된다.
- 그렇다면 외래키는 왜 many 쪽에서 가지는가?
	- db 이론적으로?
		- 그렇다면 왜 굳이 관리하는 쪽이 필요?

### References
---
[]()
https://stackoverflow.com/questions/2749689/what-is-the-owning-side-in-an-orm-mapping