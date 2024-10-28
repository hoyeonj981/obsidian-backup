created at : 2024-10-22 23:49

#### tags

#

--- 

- java 7 또는 8 기준으로 설명
- hotspot/src/share/vm/oops에 정의됨
- 전체적인 구조
	- oop (oopDesc을 정의함. 추상화된 클래스)
		- instanceOop (인스턴스 자바 객체 표현. 실제 데이터가 존재)
		- methodOop (메서드 표현형)
		- arrayOop (OopDesc를 상속하고 arrayOopDesc를 정의함. 모든 배열 구조를 표현하는 추상화된 클래스)
		- symbol (string 클래스 표현)
		- klassOop (Klass 헤더, 해당 클래스의 메타데이터를 가리키는 포인터들의 집합)
		- markOop (OopDesc 안에 존재. JVM 내부에서 객체를 마킹할 때 사용. lock, gc 등)

### References
---
[]()
자바 최적화: 가장 빠른 성능을 구현하는 검증된 10가지 기법