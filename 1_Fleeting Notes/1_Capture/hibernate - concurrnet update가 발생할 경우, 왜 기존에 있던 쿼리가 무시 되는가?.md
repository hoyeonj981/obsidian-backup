created at : 2024-06-21 14:04

#### tags

#

--- 

- 문제
	- main thread에서 em.find() 실행
	- main thread에서 엔티티 수정
	- concurrent thread에서 em.find() 실행
	- concurrent thread에서 엔티티 수정
	- concurrent thread에서 update 쿼리가 나간 후 커밋
	- main thread에서 refresh 호출, select 쿼리 나감
	- main thread 커밋
- 나의 예상
	- mian thread에서 수정한 값이 왜 update 쿼리로 안 나가는가?
	- 예상 1
		- refresh가 실행이 되어 main thread가 바라보는 값이 stale한 값임을 알게 되었다.
		- 이전에 바라보던 값을 보고 수정했기 때문에 일관성을 지키기 위해서는 update 쿼리가 나가서는 안 된다.
	- 실험 1 - refresh를 지우고 실행하면 update 쿼리가 나갈 것이다.
		- 예상과 일치. update 쿼리가 나가서 미리 커밋된 데이터는 덮어짐

### References
---
[]()
