created at : 2024-06-04 02:57

#### tags

#

--- 

- cascade 옵션은 구체적으로 어떻게 동작할까
- ManyToMany에서 양쪽에 모두 cascade 옵션을 설정하니 PersistentObjectException: detached entity passed to persist 에러가 안 생기고 정상 동작
	- stackoverflow에서는 이런 문제를 해결할 때 cascade 옵션을 양쪽으로 설정하라는 답변은 없었다.
	- 블로그에 나온 결과물도 한 쪽만 설정한다.
- 

### References
---
[]()
https://vladmihalcea.com/the-best-way-to-use-the-manytomany-annotation-with-jpa-and-hibernate/