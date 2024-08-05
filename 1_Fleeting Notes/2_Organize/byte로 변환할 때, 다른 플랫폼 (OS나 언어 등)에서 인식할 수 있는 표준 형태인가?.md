created at : 2024-05-03 13:26

#### tags

#

--- 

- 값을 byte로 변환하는 기능을 지원한다.
	- jvm에서 다른 jvm으로 전달할 수 있음. (serializable 구현으로)
		- toByte 이런 메서드가 존재한다.
	- 그렇다면 jvm에서 다른 언어(python, javascript, c 등)으로 전달 가능?
		- 불가능하다면 왜?
		- 언어별로 byte 표현 방식이 다르다는 것을 가정함.
	- jvm x86 -> jvm arm 도 가능?
		- 불가능하다면 왜?
		- 아키텍처 별로 byte 표현 방식이 다르다는 것을 가정함
	- jvm windows -> jvm linux 가능?
		- 불가능하다면 왜?
		- 운영체제별로 byte 표현 방식이 다르다는 것을 가정함.

- 굳이 protocol buffer, json 등 이런 변환없이 그냥 데이터가 Byte로 직렬, 역직렬하면 되는 거 아닌가?
	- 현실에서는 아니니까 json 이런걸 사용하는 것 같은데... 그럼 왜? POSIX처럼 표준으로 만들면 좀 더 수월하게 데이터를 전달할 수 있을 것 같은데

- 좀 더 추상화해서 설명하자면, client에서 사용하는 메모리 레이아웃이 server에서도 동일하다면, 그리고 그 방법이 표준으로 정의되어서 언어마다 지원한다면 굳이 여러 추상화 계층을 넘어가며 데이터를 직렬화할 필요가 없을 것이다.

- wikipedia
	- byte ordering 문제
		- 플랫폼마다 메모리 레이아웃, 데이터 표현 방법, byte ordering(레디안 표현법) 등이 다 다르다
	- 플랫폼 독립적으로 구성하는 것이 오히려 안정적으로 데이터를 재구성할 수 있으니 더 이득이다.
		- 단일 머신에서도 포인터는 깨지기 쉽다. (잘못된 곳을 가리킬 수 있음)

- 결론
	- 아직까진 usb c처럼 다 통일시킬 만한 직렬화 방법? 표준은 없다.
### References
---
[]()
https://docs.oracle.com/javase/6/docs/platform/serialization/spec/serialTOC.html
https://en.wikipedia.org/wiki/Comparison_of_data-serialization_formats
https://en.wikipedia.org/wiki/Serialization