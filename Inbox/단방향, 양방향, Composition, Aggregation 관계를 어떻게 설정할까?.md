created at : 2024-04-28 03:41

#### tags

#

--- 

- 단방향, 양방향, composition, aggregation이 무슨 말인지 알겠다.
- 그렇다면 적절한 설계는 어떻게 할까?

- 처음에는 Value object로 설정했는데, 나중에 entity로 만들 필요성이 생길 수 있다.
	- 어떤 경우에?
		- 단방향에서 양방향으로 만들 필요성이 생겼다.
			- item -> bid -> user
			- item -> bid <-> user
	- 일단 모든 것을 value object로 만들고 꼭 필요한 것만 entity로 승격한다.
	- entity의 연관 관계는 단순하게 만들어야 한다. (가능한)
		- 어떻게?
			- value object에 대한 공유 참조를 피한다.
				- 하나의 객체를 여러 객체가 참조하는 모양은 피한다.
				- 필요하다면 차라리 각자 value object로 만들어서 가지고 있는다.
				- setter를 만들지 않는다.
			- life cycle 의존성
				- value object는 entity가 삭제되면 같이 삭제되어야 한다.
				- 만약 복사본이 필요하다면, reference를 넘기는 것이 아닌 복사본을 만들어서 넘겨줘야 한다.

### References
---
[]()
