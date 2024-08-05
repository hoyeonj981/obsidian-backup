created at : 2024-05-05 04:16

#### tags

#

--- 

- first level cache
	- 하나의 session에 존재하는 캐시
		- entityManager or session(hibernate) 단위에서 로드된 엔티티에 대한 캐시
		- commit될 때 한번에 반영
- second level cache
	- 애플리케이션 단위로 존재하는 캐시
		- entityMangerFactory or SessionFactory(hibernate) 단위에서 관리하는 캐시
		- 모든 entityManger or session에서 사용할 수 있다.
	- 만약 인스턴스가 이미 first level cache에 존재할 경우, first level에서 조회 후 전달
	- 만약 first level에 없다면, second level에 존재하는 지 확인. 업데이트를 second level에 반영 후 전달
	- 만약 어떤 캐시에도 없다면 데이터베이스에서 로드 후 전달한다.
- hibernate의 second level cache는 cache provider(ehcache, caffeine 등)를 알 수 없도록 설계됨.
	- hibernate는 RegionFactory 인터페이스만 구현했다.
	- second level cache는 cache provider가 필요한 것 같다.
- 이걸 왜 쓰는가?
	- DB에 접근하는 횟수를 줄이기 위해서
	- 결국 무엇을 캐시하느냐가 중요하다
- 유용한 경우
	- hibernate를 사용해서 db에 쓰기 작업할 때
	- 자주 읽는 객체가 있을 경우
	- db가 1개 뿐이고 replication이 없는 경우, 결국 읽기 작업을 담당할 수 있는 노드가 없다면 고려해볼 사항임.
		- 분산 캐시가 필요하다면 JGroups를 사용하라
- 사용할 수 없는 경우
	- ID를 key 값으로 설정하기 떄문에 ID가 없다면 select할 수 없다.
		- 객체를 저장하는 것이 아니라 데이터를 저장하는 형태이다.
	- HQL (JPQL)
		- 쿼리를 작성해서 검색할 경우 사용할 수 없음. (위와 같은 경우)
	- fetch="join"에서는 사용 불가
		- fetch="select"에서만 사용 가능
		- 연관 데이터를 전부 불러오기 때문인 듯
- query cache
	- HQL 쿼리에 대한 결과를 캐싱할 수 있다.
	- 변경이 잘 없는 것을 캐싱하는 것이 효과적

### References
---
[]()
https://stackoverflow.com/questions/7058843/when-and-how-to-use-hibernate-second-level-cache
https://www.baeldung.com/hibernate-second-level-cache