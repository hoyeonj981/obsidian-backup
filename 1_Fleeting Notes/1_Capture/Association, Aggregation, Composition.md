created at : 2024-04-19 17:58

#### tags

#

--- 

- 실제 코드로 나타낼 경우 어떻게 나타낼 수 있는가?
- life cycle이 같거나 다르거나, 이걸 어떻게 구별?
- 객체를 설계할 때 어떻게 적용해야 하는가?

- association
	- 객체지향 설계 시 만들어지는 객체들 간의 관계
		- 하나의 객체가 다른 객체의 기능을 사용할 때 만들어짐.
	- composition, aggregation으로 나타낼 수 있다.
	- has-a 관계를 뜻한다.
		- is-a 는 상속을 나타냄
	- one-to-one, one-to-many, many-to-many 연관으로 나타낼 수 있다.
	- composition
		- life cycle이 강하게 결합된 상태를 나타냄.
		- 소유자가 사라지면, 연관된 객체들도 함께 사라진다.
		- 소속된 객체는 한 객체 안에서 귀속된 상태로만 존재한다. (따로 존재하지 않음)
		- 궁금증
			- 반드시 소유자의 생성자가 호출될 때, 소속된 객체들도 함께 생성이 되어야 하는가?
				- 그렇다면 외부에서 객체를 생성할 필요가 없을 것이다.
	- aggregation
		- composition처럼 다른 객체들의 레퍼런스를 가지고 있지만, life cycle까지 동일하지 않다.
		- composition보다 느슨한 관계
		- 소유자가 사라지더라도 소속된 객체들은 살아있을 수 있다.

- composition, aggregation 언제 어떻게 구별해서 사용해야 하는가?
	- User 객체
		- compositon (1:N) BillingDetails
			- BillingDetails 객체는 User 객체에 강하게 결합
			- User 객체가 없다면 BillingDetails 객체도 없다.
		- Aggregation (1:N) Address
			- Address는 주소이기 때문에 User가 없어도 독립적으로 존재할 수 있다.
			- 다른 시스템에서 Address 객체를 사용할 수 있다.
### References
---
[]()
https://algodaily.com/lessons/association-aggregation-composition-casting/java/realization
https://medium.com/@francois.paupier/effective-domain-modeling-170ad3972fb6
자바 퍼시스턴스 프로그래밍 완벽 가이드 (p.10)