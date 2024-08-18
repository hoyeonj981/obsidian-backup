created at : 2024-05-28 17:07

#### tags

#

--- 

- 외래키는 null일 수 있다.
	- 외래키는 부모 테이블에 키가 존재할 것이라고 가정한다.
	- null의 의미는 '아직 값이 정해지지 않았다' 의미, 값이 '없다' 의미가 아니다.
```
CREATE DATABASE t;
USE t;

CREATE TABLE parent (id INT NOT NULL,
                     PRIMARY KEY (id)
) ENGINE=INNODB;

CREATE TABLE child (id INT NULL, 
                    parent_id INT NULL,
                    FOREIGN KEY (parent_id) REFERENCES parent(id)
) ENGINE=INNODB;


INSERT INTO child (id, parent_id) VALUES (1, NULL);
-- Query OK, 1 row affected (0.01 sec)


INSERT INTO child (id, parent_id) VALUES (2, 1);

-- ERROR 1452 (23000): Cannot add or update a child row: a foreign key 
-- constraint fails (`t/child`, CONSTRAINT `child_ibfk_1` FOREIGN KEY
-- (`parent_id`) REFERENCES `parent` (`id`))
```

- 예시
	- item - item_buyer - user
		- user는 아이템을 구매할 수도 있고 아닐 수도 있다.
		- item_buyer
			- pk, fk : item_id
			- fk : buyer_id
				- 위 테이블 구조일 경우 아이템은 존재하나 구매자가 없을 수 있다.

- null일 수 있다면, 애플리케이션에서 체크할 필요가 있지 않을까?
	- 0 to many 관계일 경우
	- 가져온 데이터 중에서 null이라면 npe가 발생하지 않을까?
- 
### References
---
[]()
https://stackoverflow.com/questions/7573590/can-a-foreign-key-be-null-and-or-duplicate
https://stackoverflow.com/questions/2366854/can-table-columns-with-a-foreign-key-be-null