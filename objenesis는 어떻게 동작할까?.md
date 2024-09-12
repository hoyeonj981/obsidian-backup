created at : 2024-09-10 09:18

#### tags

#

--- 

- 인스턴스를 생성해주는 객체 ObjenesisStd
	- InstantiatorStragey에게 객체 생성을 위임한다.
	- InstatiatorStragey는 PlatformDescription에 의해 플랫폼에 맞는 객체 생성기(?)를 만든다.
		- gcj, perc, sun, android
- 왜 이런 라이브러리를 개발했는가
	- reflection을 사용해서 만들면 귀찮은 여러 작업들이 있다.
	- 이걸 대신해주는 라이브러리
	- 구체적으로 어떤?
		- 필드를 초기화하지 않는다.
		- final, args가 있거나 기본 생성자가 없는 경우 객체를 생성해준다.
	- 여기서 객체를 생성할 때는 해당 클래스에 있는 생성자를 사용하지 않고 Object의 기본 생성자를 사용한다.
		- 그렇다면 Object으로 생성했는데 어떻게 해당 클래스 타입에 맞게 캐스팅이 진행되는가
			- 클래스 타입 정보를 파라미터로 전달하고, ReflectionFactory에서 Object 생성자를 통해 내가 전달한 클래스의 생성자를 만듦.
			- 해당 생성자를 통해서 내가 원하는 클래스가 생성이 된다.
				- 단, 멤버변수들이 초기화가 안 되었기 때문에 값을 비교하는 것은 할 수 없음.

### References
---
[]()
https://stackoverflow.com/questions/95419/what-are-all-the-different-ways-to-create-an-object-in-java