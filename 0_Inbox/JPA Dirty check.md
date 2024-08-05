created at : 2024-04-23 17:58

#### tags

#

--- 

- 레퍼런스 타입은 얕은 복사일 경우 dirty check이 2번 발생
	```
	@Test  
    void referenceTypeDirtyCheckTest() {  
        try (SessionFactory sessionFactory = createSessionFactory();  
             Session session = sessionFactory.openSession()) {  
            session.beginTransaction();  
  
            var holder1 = new Container(10);  
            var holder2 = new Container(holder1.getValueIntObject());  
            session.persist(holder1);  
            session.persist(holder2);  
  
            // holder1.setValueIntObject(20);  
            holder1.setNewValueIntObject(20);  
  
            session.getTransaction().commit();  
        }  
    }
	```
```
@Entity  
public class Container {  
  
    @Id  
    @GeneratedValue(strategy = GenerationType.IDENTITY)  
    private Long id;  
  
    @Embedded  
    private ValueIntObject valueIntObject;  
  
    public Container(final int value) {  
        this.valueIntObject = new ValueIntObject(value);  
    }  
  
    public Container(final ValueIntObject valueIntObject) {  
        this.valueIntObject = valueIntObject;  
    }  
  
    public ValueIntObject getNewValueIntObject() {  
        return new ValueIntObject(this.valueIntObject.getAnInt());  
    }  
  
    public ValueIntObject getValueIntObject() {  
        return valueIntObject;  
    }  
  
    public void setNewValueIntObject(final int anInt) {  
        this.valueIntObject = new ValueIntObject(anInt);  
    }  
  
    public void setValueIntObject(final int anInt) {  
        this.valueIntObject.setAnInt(anInt);  
    }  
}
```
```
@Embeddable  
public class ValueIntObject {  
  
    private int anInt;  
  
    public ValueIntObject() {  
    }  
    public ValueIntObject(final int anInt) {  
        this.anInt = anInt;  
    }  
  
    public int getAnInt() {  
        return anInt;  
    }  
  
    public void setAnInt(final int anInt) {  
        this.anInt = anInt;  
    }  
  
    @Override  
    public String toString() {  
        return "ValueIntObject{" +  
                "value=" + anInt +  
                '}';  
    }  
  
    @Override  
    public boolean equals(final Object o) {  
        if (this == o) return true;  
        if (o == null || getClass() != o.getClass()) return false;  
        ValueIntObject that = (ValueIntObject) o;  
        return anInt == that.anInt;  
    }  
  
    @Override  
    public int hashCode() {  
        return Objects.hash(anInt);  
    }  
}
```

- dirty check은 구체적으로 어떤 방법으로 동작할까
	- 프록시 기반이라면 해당 객체에 접근한 값만 바뀌면 dirty check이 발생해야 하는데, 서로 다른 프록시가 생성되어도 어떻게 2번의 update 쿼리가 나가는가?
	- 값이 변경되었다는 것을 어떻게 인지할까?
		- hash, equals?
			- 엔티티의 equlas가 아닌 내부적으로 구현된 isEquals를 사용한다.
				- 기본타입일 경우 equals와 비슷한 로직으로 진행
				- 만약 Component type일 경우 내부를 한 번 더 스캔해서 하나씩 비교해서 dirty checking을 한다.
		- event가 발생하는 것을 감지한다. (flushEntityEvent)
		- 
	- 진행
		- 엔티티 메타 데이터를 가져온다.
			- 메타 데이터에는 엔티티 프로퍼티들이 존재
		- 현재 로드된 값과 변경된 값을 비교한다.
		- 변경된 값이 존재할 경우 배열에 기록해서 리턴, dirty check flag를 올린다.
		- 현재 동작하는 thread가 아닌 이벤트 listener에서 직접 관리하는 것 같다.
	- 엔티티가 너무 많이 로드된 상황에서는 Dirty check이 문제가 될 수 있을 것 같다.
		- 퍼포먼스 향상을 어떻게?
### References
---
[]()
https://vladmihalcea.com/hibernate-event-listeners/
https://stackoverflow.com/questions/5268466/how-does-hibernate-detect-dirty-state-of-an-entity-object