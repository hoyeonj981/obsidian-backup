created at : 2024-03-12 02:52

#### tags

#DesignPattern 

--- 

- 이 개념이 jdbc, jpa repository 등 프레임워크에서 제공하는 개념에 어떻게 적용되어 있는가?

- ejb
	- 모듈러 기업소프트웨어 구축을 위한 자바 API들 중 하나.
	- 서버 사이드 소프트웨어들의 비지니스 로직을 캡슐화한 애플리케이션 (흔히 백엔드라고 불리는 것)
	- ejb를 관리하는 것이 ejb 컨테이너. 컨테이너를 사용으로 제공하는 다양한 벤더들이 있다.
	- 트랜잭션, 동시성 제어, 잡 스케줄링 보안 등 여러 기술들을 제공함.
- ejb 컨테이너에서 다루는 대표적인 beans
	- 상속해서 구현해야 함.
	- session beans
		- 같은 jvm 안에서, 서로 다른 jvm끼리 인터페이스 혹은 인터페이스 없이도 접근할 수 있는 빈들.
		- stateful, stateless, singleton으로 구분됨.
	- entity beans
		- 데이터 베이스에서 관리되는 데이터를 표현하는 빈.
			- 빈의 내용이 데이터베이스와 일치한다.
		- 빈이 직접 영속성을 관리하는 BMP, 컨테이너에게 위임한 형태인 CMP로 나뉜다.
		- 그리 널리 쓰이지 않고 선언적 트랜잭션을 사용하는 CMT 기반의 Session beans가 널리 쓰였다.
	- CMT 기반 session beans가 널리 쓰이니 비지니스 로직에 데이터 접근 로직이 침투하게 됨.
		- 이걸 분리하고자 등장한 패턴이 DAO 패턴
	- DAO
		- 데이터 접근 로직을 담당하는 객체.
		- entity beans의 기능을 대체하기 때문에 DAO는 하나의 테이블에 접근하는 기능을 제공하는 것일까?
- dao? repository
	- DAO는 entity beans을 대체하기 위해서 등장했기 때문에? 테이블 당 1개로 만듦?
	- repository는 객체 컬렉션(객체의 집합) 처리에만 집중한다.
	- repository는 aggregate 루트 당 1개이며 aggregate와 관련된 동작만 지원
		- 개발자는 구현체에 대해서 신경 쓰지 않는다. redis에 저장되던 db에 저장되던 nosql이던 그건 구현체가 알아서 할 일이고 비지니스 로직에만 집중할 수 있게 한다.
		- 이미 메모리에 존재하는 것처럼 다룬다.
	- repository 구현체가 여러 테이블에 접근할 필요가 있다면 repository는 여러 dao, 데이터 소스 등 여러 조합으로 구성될 수 있다.
		- unit of work 패턴 활용
			- 비지니스 레이어에서도 트랜잭션처럼 일련의 원자적 연산이 필요할 수 있다.
			- 이것을 추상화해서 제공하는 것이 unit of work 패턴
			- unit of work 패턴이 여러 레파지토리를 사용해서 동작할 수 있다.
- 실무에서는 repository가 사실상 dao와 동일하게 다뤄지는 것 같다.
	- JpaRepository  == JpaDao

### References
---
[dao vs repository - github](https://github.com/msbaek/memo/blob/master/dao-vs-repository.md)
https://en.wikipedia.org/wiki/Entity_Bean
https://en.wikipedia.org/wiki/Jakarta_Enterprise_Beans
https://m.blog.naver.com/sillllver/220593543939
https://martinfowler.com/eaaCatalog/repository.html