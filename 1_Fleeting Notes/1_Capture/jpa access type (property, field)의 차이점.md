created at : 2024-04-30 17:40

#### tags

#

--- 

- property, field?
	- 정확한 정의는 없는 것 같음. 문서마다 관용적으로 사용하는 것 같다.
	- property
		- private member + getter + setter
		- getter, setter에 더 의미가 있는 것 같다. 어차피 필드에 접근하기 위해서는 이 메서드를 통해서 접근하기 때문인 듯
	- field
		- 클래스에 선언된 private member

- property and field in jpa
	- property
		- getter, setter가 없으면 런타임에 에러가 난다.
		- getter, setter 메서드 위에다 선언할 수도 있다.
		- getter, setter 메서드 위에다가 선언할 수도 있다
			- setter만 있고 getter가 없고 setter 위에다가 선언해도 동작함
			- getter만 있고 getter 위에 선언하면 예외 발생
				- getter 위에 선언하고 setter도 있으면 동작함
		- 아마 프록시 기반으로 동작해서 그런 것 같다.
			- 필드 값을 할당하기 위해서는 setter를 통해서 접근하는 듯
			- 근데 setter에는 지원되지 않는다고 명시되어 있다.
				- 그렇다면 setter 위에 있으면 그냥 field로 인식하나?
	- field
		- 그냥 field를 통해서 접근
	- 일반적으로 @Id 애노테이션이 어떻게 설정되었는지에 따라서 암묵적으로 결정된다.
		- @Id가 필드 위에 있다면 모든 access type이 field로 지정됨
		- embeddable 타입들도 부모 엔티티의 access 전략을 그대로 상속함
			- 명시적으로 다른 전략을 선택할 수도 있다.
	- 왜 이런 차이를 만들었는가
		- 최적화 여지를 만들 수 있다고 한다.
			- 어떻게?
				- 

- 일반적으로 field 방식이 더 추천되는 것 같다.
	- 메서드로 필드에 유연하게 접근할 수 있는데, 이걸 굳이 jpa 스펙 때문에 유연성이 떨어짐
	- pojo에 orm 기술에 관한 코드가 들어가게 됨
		- getter에 orm에 관련된 코드가 들어갈 여지가 있다.
	- 내가 굳이 getter, setter를 제공하지 않을 수도 있음.
		- property는 필수로 넣어야 한다.
	- 역직렬화할 때 편리하다.
		- 필드에 해당되는 값으로 직렬화하면 된다.
		- 메서드로 이것저것 추가된 값이라면 직렬p화할 때 어려울 수 있음.

### References
---
[]()
https://docs.jboss.org/hibernate/orm/6.4/userguide/html_single/Hibernate_User_Guide.html#access
https://happy-coding-day.tistory.com/entry/JPA-field-or-property-access
https://stackoverflow.com/questions/594597/hibernate-annotations-which-is-better-field-or-property-access
https://dzone.com/articles/12-feb-jpa-20-why-accesstype
https://dzone.com/articles/jpa-implementation-patterns-7
https://chstath.blogspot.com/2007/05/field-access-vs-property-access-in-jpa.html