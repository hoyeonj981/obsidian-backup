created at : 2024-06-03 19:26

#### tags

#

--- 

- insert 다음에 update 쿼리가 발생하는 경우가 있다.
	- 왜 수정된 정보를 update하는 것일까?
- 내가 생각하는 동작 방법
	- entity가 em에 의해서 관리가 되고, 만약 새로운 데이터를 넣게 될 경우 트랜잭션이 끝나기 전에 발생한 수정은 전부 무시하고 마지막으로 수정된 값으로 insert 쿼리를 실행하는 것이 효율적인 동작
- 발생하는 경우
	- OneToMany 관계에서 JoinColumn을 사용할 때, insertable, updatable이 false가 아니라면 insert 다음에 update가 발생한다.
- insertable, updatable이 왜 필요한가?
- update after insert는 왜 발생하는 것일까?

### References
---
[]()
https://stackoverflow.com/questions/25379046/why-hibernate-generates-insert-and-update-for-onetomany-mapping