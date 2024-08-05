created at : 2024-05-08 07:20

#### tags

#

--- 

- save를 호출하면 insert만 발생해야 하는데, update가 발생한다.
	- update가 발생하는 컬럼은 convert 애노테이션을 붙인 곳이다.
	- convert 시 발생하는 것인가?
	- 그렇다면 왜 이런 문제가 발생할까?
- 추상타입을 사용할 경우 dirty check이 발생한 것을 확인
	- 그냥 일반 타입은 문제가 없었다.
	- 왜 이런 메커니즘으로?
	- serializable을 붙이니까 update 쿼리가 없어짐
		- 근데 defaultColumn은 계속 update 쿼리가 날아감.
		- 그렇다고 converter가 호출이 안 되는 것은 아님
		- 도대체 왜 영속될 때 호출이 되는가
		- 또 serializable이 있다면 왜 또 update가 호출이 안 되는가
	- 2차 캐시와 관련이 있나?
	- 

### References
---
[]()
https://kwonnam.pe.kr/wiki/java/hibernate/dirty_checking
https://vladmihalcea.com/jpa-attributeconverter/