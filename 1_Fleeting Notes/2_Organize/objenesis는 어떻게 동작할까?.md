created at : 2024-09-10 09:18

#### tags

#Java #OpenSource

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
- 어떻게 자신의 생성자가 아닌데, 다른 생성자를 통해서 객체를 생성할 수가 있나?
	- 자세한 구현은 MethodAccessorGenerator 안에 있다.
	- 상수와 bytecode를 조작하는 것 같은 코드가 내부에 package-private으로 존재한다.
	- 구글에 검색해도 내부 구현이라서 자세한 문서도 없고 코드 주석도 자세한 설명이 없음.
	- 메서드 이름으로 유추해보니 아마 다른 클래스에서 호출할 생성자를 연결해주고 해당 타입으로도 캐스팅이 이루어지는 것으로 추측됨.
- generic 클래스인 경우 어떻게 되나?
	- geneics 정보가 런타임에는 유지가 안 된다.
	- 런타임에 정보를 알 수가 없음.
- 왜 이런 기능이 필요할까?
	- 장점
		- 기본 생성자가 있든 없든 일단 만들 수 있다.
		- 사이드 이펙트가 있는 생성자라도 상관없다.
		- 예외가 발생하는 생성자라도 상관없다.

### References
---
[]()
https://stackoverflow.com/questions/95419/what-are-all-the-different-ways-to-create-an-object-in-java