created at : 2024-04-03 08:44

#### tags

#

--- 

- service?
	- 잘 정의된 인터페이스와 추상 클래스, 일반 클래스의 집합
	- 간단하게 API
- service provider?
	- 특정 서비스의 구현체
	- 즉, 인터페이스를 구현한 클래스 or (추상일 수도 있는) 클래스를 상속해 구현한 클래스
- service loader
	- 애플리케이션이 런타임 환경에서 service provider를 찾아 로드하기 위한 객체
- jar? war?
	- jar
		- java archive, 배포를 위한 하나의 파일인 패키지 파일 포맷
		- metadata + resources(txt, images ...) + java class file (bytecode)
		- META-INF/MANIFEST.MF 안에 jar 파일를 어떻게 사용할지 기술되어 있다. (포함될 수도 있음)
		- jar 파일은 실행이 가능한데, manifest 안에 entry point 클래스가 기술되어야 함.
			- Main-Class:myProgram.MyClass 안에 있는 main 함수가 실행되어야 할 것.
	- WAR
		- web application archive
		- JAR(s) + XML + JSP + Java Servlet + java class file + html 등
		- 웹 애플리케이션 배포를 위한 위 구성 요소들의 집합 포맷
		- JAR와 비슷하지만 차이가 있는 것
			- /WEB-INF
				- web.xml이 포함되어 있다. 웹 애플리케이션의 구조를 정의하는 파일
		- 작은 부분을 수정하면 전체를 다시 컴파일해서 배포해야 함.
			- JAR처럼 부분을 나눠서 배포할 수 없음.
- java classloader
	- JVM에서 자바 코드를 실행할 때 클래스 로딩을 담당
	- 사용자 정의 classloader도 작성할 수 있다.
		- class loader는 자바로 작성되었기 때문에
		- 가능한 것
			- 동적으로 로드와 언로드가 가능함. (라이브러리, 심지어 HTTP 자원까지도 가능)
				- Jython 같은 동적 스크립트 언어
				- bean builder들
			- bytecode를 로드 되었을 때 바꿀 수 있다. (바이트코드 암호화)
			- bytecode를 로드 되었을 때 변조가 가능함. (load-time weaving)
	- JAR hell
		- 버전이 다른 두 라이브러리가 설치되었다면 어느 것이 로딩될 지 알 수 없음.
		- 여러 라이브러리, 애플리케이션에서 서로 다른 foo 라이브러리를 참조하고 있다면, 어떤 버전의 foo가 로드되어 사용되는지 알 수 없음
		- 여러 JAR들이 복잡하게 엮인 상태에서 여러 클래스 로더에 의해 무엇이 로드되었는지 파악하기 어려울 수 있다. -> 무엇에 의해 버그가 발생했는지 파악하기 어렵다.
		- Java platform module system으로 이 문제를 해결
			- module-info.java에 의존관계에 대해 정의
			- 하지만 서로 다른 버전에 대한 라이브러리 공존에 대해서는 해결하지 못함.
- service loader와 java classloader는 무슨 차이?
	- service loader는 meta-inf/service를 읽어서 로드를 해주는 것 같다.
		- service - service provider 이런 식으로 상속구조를 갖추지 않아도 되는 것 같다.
		- 그냥 service provider가 추상 클래스로 기본 기능과 확장 가능하게 만들어서 해당 기능을 컴포넌트로 사용하는 방식으로 구현되어 있다.
	- 근데 굳이 그럴 필요가? 그냥 class loader가 있는데
	- service loader 내부에서도 class loader를 사용한다.
	- class loader
		- jvm 구동 중에 클래스를 로드한다.
	- service loader
		- portable한 구동을 위해서 따로 배포되는 jar 파일을 동적으로 로드한다.
			- 서로 다른 패키지로 분리되어 배포가 될 때 다형성을 사용하는 방법?
- spring boot는 auto configuration을 지원한다.
	- spring boot도 여러 모듈로 나눴는데, 이걸 어떻게 하나로 맞춰서 동작할까?
	- spring boot에서 지원하는 것을 추가하는 것만으로 자동으로 사용할 수가 있다.
	- service loader를 사용하는 것일까? 아니면 스프링에서 따로 구현한 방법이 있는 것일까?
- 스프링에서 지원하는 load factory 방식은 1.5를 기반으로 작성되었다. service loader는 1.6부터 사용되었다. 스프링은 표준 방식대로 확장하지 않고 자기 방식대로 확장이 가능함. 아직까진 우선순위에 밀려 있어 적용이 안 되는 것 같다.
### References
---
[]()
https://en.wikipedia.org/wiki/JAR_(file_format)
https://en.wikipedia.org/wiki/Java_Classloader#JAR_hell
https://en.wikipedia.org/wiki/WAR_(file_format)
https://escuela-tech.medium.com/java-serviceloader-explained-4943c060d7f6
https://github.com/spring-projects/spring-framework/issues/27753