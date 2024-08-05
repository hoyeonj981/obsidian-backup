created at : 2024-04-23 16:48

#### tags

#

--- 

- properties도 final이면 안 된다.
	- 굳이? 우회할 방법은 존재하나 왜 강제하는가?
	- -> 상관없다
		- 기본 생성자가 존재하면 그걸 사용하고 없다면 interceptor를 사용한다.

### References
---
[]()
https://javarevisited.blogspot.com/2016/01/why-jpa-entity-or-hibernate-persistence-should-not-be-final-in-java.html
https://stackoverflow.com/questions/2935826/why-does-hibernate-require-no-argument-constructor