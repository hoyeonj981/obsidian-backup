created at : 2024-05-22 21:46

#### tags

#

--- 

- @JoinColumn
	- 엔티티에 설정이 되었다고 해서 해당 컬럼이 실제 데이터베이스에 똑같이 존재할 지 알 수 없다.
	- JPA 2.x 부터 지원했음
- one to many 관계
	- jpa에서는 항상 many 쪽이 외래키 컬럼을 가진다.
		- 왜?
			- 데이터베이스 무결성
				- 중복을 줄이고 정규화를 만족하기 위해서
	- 과거 JPA 1.x 때는 OneToMany는 항상 JoinTable이 존재해야 했다.
		- 이 경우 ManyToMany와 유사할 수 있다.
			- many에 해당되는 key 값을 유니크하게 설정할 경우 ManyToMany와 다르게 관리할 수 있다.
- @ManyToOne
	- relationship owner를 나타낸다. 

### References
---
[]()
https://stackoverflow.com/questions/11938253/jpa-joincolumn-vs-mappedby
https://en.wikibooks.org/wiki/Java_Persistence/OneToMany#Unidirectional_OneToMany.2C_No_Inverse_ManyToOne.2C_No_Join_Table_.28JPA_2.0_ONLY.29
https://stackoverflow.com/questions/37542208/what-is-joincolumn-and-how-it-is-used-in-hibernate