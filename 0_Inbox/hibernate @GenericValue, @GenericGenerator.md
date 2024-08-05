created at : 2024-04-29 17:10

#### tags

#

--- 

- GenericValue
	- 반드시 @Id 어노테이션이 있어야 함.
	- 해당 Id 값을 어떻게 generate할지 결정할 수 있다.
- GenericGenerator
	- custom generator를 설정할 때 사용

- 이것들 어떻게 동작할까
	- reflection? weaving?
	- 추측 : 이것들 일단 프록시 기반으로 동작할 것 같다.

 - hi / lo algorithm?
 - enhanced sequence?
- 일반적으로 pre-insert 생성 전략을 사용한다.
	- id 값을 persist 된 후에 할당하고 commit 직전에 insert 쿼리가 한 방으로 나간다.
	- 만약 테이블에서 id 값을 가져와야 한다면 해당 테이블은 다른 서버들과 공유가 되어야 할 것이다.
	- 클러스터링이 된 환경에서 jpa는 어떻게 사용해야 할까?
		- hibernate의 기능 활용? jpa 표준 사용?
		- 어떤 경우가 있을 지 실제 사례를 확인
			- 여러 데이터베이스에서 생성된 기록을 하나의 테이블로 기록해야 할 경우
				- 뭔가 넓은 범위의 통계를 만들기 위해서?
				- 구글 계정 같은거?
					- 구글 코리아, 구글 usa에서 만들어진 계정은 각 국가마다 따로따로 보관하겠지만, 결국 1개의 계정으로 구글 서비스를 다 이용할 수 있으니 언젠가 merge할 떄가 있을 것 같다.
		- 성능에 대한 고민은?
### References
---
[]()
https://stackoverflow.com/questions/18205574/difference-between-generatedvalue-and-genericgenerator
https://vladmihalcea.com/from-jpa-to-hibernates-legacy-and-enhanced-identifier-generators/P