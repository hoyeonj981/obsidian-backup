created at : 2024-05-05 22:23

#### tags

#

--- 

- sql view
	- 하나 이상의 테이블에서 원하는 모든 데이터를 선택하여 나타낸 것
	- 테이블처럼 column과 row가 있다.
	- 따로 데이터를 포함하는 것이 아님. 그냥 보여주는 것일 뿐. (일종의 캐시?)
	- 조회된 쿼리 결과를 테이블처럼 보여주는 것
- sql view의 장점은?
	- 레거시 테이블을 조회용으로 남길 필요가 있다면 view로 만들어서 보관한다.
		- 오래된 T_OLD 테이블이 있다. 이건 더이상 사용하지 않음. 사용자 정보가 있는 테이블
		- T_NEW는 T_OLD에서 active 컬럼이 추가됨.
		- 기존 T_OLD를 조회하던 쿼리를 모두 T_NEW로 옮기는 것은 위험하다.
			- 기존 쿼리도 바꿔야 하고 테스트하고 배포하는 과정에서 문제가 생길 여지가 있다.
		- T_NEW 테이블을 통해서 T_OLD 테이블을 흉내내는 view를 만든다.
			- select (active를 제외한 모든 컬럼) from T_NEW where active = 1
		- 기존에 있던 T_OLD는 drop
	- 보안성 강화
		- 조회말고 테이블에 영향을 줄 수 없다.
		- 보여주지 않을 컬럼을 숨길 수 있음.
	- 간편함
		- 복잡한 쿼리를 단순하게 만들 수 있다
		- 컬럼 이름을 바꾸거나 굳이 필요없는 컬럼을 포함하지 않거나
	- 조합
		- 임시 테이블처럼 만들어서 사용할 수 있다.
		- 여러 테이블을 조합해서 하나의 테이블처럼 사용할 수 있다.
- sql view를 대체할 수 있는 방법들
	- view를 query cache로 대체할 수 있다고 생각했는데 생각과 아닌 듯.
		- view는 쉽게 말해 조회만 가능한 테이블
		- cache는 임시 저장소이기 때문에 오래된 데이터가 퇴출돼야 함.
	- view와 cache는 다르다.
- view는 어떻게 관리될까
	- index, 저장 방식 등
- materialized view

### References
---
https://www.datacamp.com/tutorial/views-in-sql
https://en.wikipedia.org/wiki/View_(SQL)
https://stackoverflow.com/questions/2680207/what-is-a-good-reason-to-use-sql-views