created at : 2024-04-05 12:22

#### tags

#

--- 

- 스프링 빈 종류
	- bean definition
		- application
			- application에서 핵심 부분. 주로 사용자 정의 빈 또는 상응하는 빈들
			- service, controller 등
			- dispatcherServlet, dataSource등
		- support
			- 더 큰 설정을 위해서 지원하는 빈을 나타냄
			- ComponentDefinition 외부에 간련된 작업들
			- 빈을 굳이 이렇게 나눈 의도가 모호함.
			- 애초에 Outer ComponentDefinition 이게 뭔가?
		- infrastructure
			- 프레임워크 내부에 관련된 작업을 위한 빈들
			- ComponentDefinition 내부와 관련 작업들

- support에 해당하는 빈을 찾지 못했다.
	- ComponentDefinition Outer란 의미가 어떤 의미인지 파악이 안 됨.
	- 코드로 로드된 빈들을 찾아봐도 support role인 빈들을 찾을 수 없었다.

```
@Autowired  
ApplicationContext applicationContext;  
  
@Test  
void findApplicationBean() {  
    String[] beanDefinitionNames = applicationContext.getBeanDefinitionNames();  
    GenericApplicationContext ctx = (GenericApplicationContext) applicationContext;  
  
    for (String beanDefinitionName : beanDefinitionNames) {  
       var beanDefinition = ctx.getBeanDefinition(beanDefinitionName).getRole();  
       if (beanDefinition == BeanDefinition.ROLE_APPLICATION) {  
          System.out.println(beanDefinitionName+ " : " + beanDefinition);  
       }  
    }  
  
}
```

- 코드 상에서는 application에 관련된 빈들 (내가 정의하거나 사용자 정의 빈과 상응하는 빈들 모두가 application bean이다.)
	- 내가 만든 controller, service, 등도 application이다
	- 위 빈을 사용하기 위한 dispatcherServlet, datasource 등 이런 것도 application bean이다
- 컨테이너에서 내부적으로 동작하기 위해서 필요한 빈들은 infrastructure로 등록된다.
	- postprocessor, publisher, listener, properties 등
	- internal 패키지로 의미를 나타내는 것도 많음.

- conditional 어노테이션, condition 인터페이스, import 어노테이션
	- import
		- 임포트할 수 있는 1개 이상의 component 클래스를 나타낸다.
		- xml으로 등록하는 것과 동일하다.
			- 과거에 xml가 대세가 될 것이라 생각했는데 자바 기반 어노테이션이 대세라서 어노테이션을 통한 지원 방법
	- conditional
		- 스프링 빈을 등록할 때 condition을 구현하는 클래스가 true일 경우 해당 빈이 등록이 된다.
		- conditional() 안에 classpath를 명시해야 한다.


- conditional, import를 통해서 조건에 따라 자동으로 빈을 등록하는 것은 이제 알겠다.
	- 그렇다면 어떤 순서로 찾아서 등록을 할까?
	- autoconfiguration의 초기화 순서는 어떻게 진행될까?
		- 아마 SpringbootApplication에서 시작할 것 같은데
			- 이 어노테이션은 언제 호출이 될까
			- 

- springboot application 순서
	- main 메서드가 있는 클래스 정보와 args 를 SpringBootApplication 클래스로 전달한다.
	- SpringFactoriesLoader에 의해 **META-INF/spring.factories** 경로에서 자동으로 등록할 빈 목록을 읽어 온다.
	- 목록을 하나씩 순회하며 jar 파일을 탐색
	- 해당하는 jar에 인터페이스(추상 클래스)와 구현체가 매핑이 되어있는 정보가 있다. 그 정보를 properties로 읽어 들인다.
	- 인터페이스(추상클래스) - 구현체를 hashmap으로 매핑해서 저장한다.
		- 인터페이스 - 구현체는 1:1 or n일 수 있다.
	- 이렇게 찾은 관계는 캐시에 저장된다. (static에 맵 형태로 존재함)
	- bootstraper가 없다면 빈 LinkedHashSet이 반환된다.
		- 왜 boostrapper가 존재하는가?
		- 구현체도 없고 그냥 메소드 1개인 인터페이스.
		- 무슨 용도?
	- initalizer 클래스 정보를 넘겨서 위 과정을 다시 반복한다.
		- bootstraper -> initalizer 순서로 진행됨.
			- 순서에 의미가 있는가?
		- 이 과정도 인터페이스(추상클래스) : 구현체 관계를 맵으로 등록한다.
		- 클래스 로더의 정보는 왜 필요할까?
			- 계속해서 클래스 로더의 정보를 가지고 다닌다.
	- listener도 위 과정 반복한다.
	- main 함수가 실행된 클래스 정보를 등록한다.
	- 초기화 작업 진행
		- headless mode
			- 그래픽 화면이 없는 모드
			- 왜 spring은 awt가 필요한가?
				- awt는 abstract window toolkit이다.
				- 스프링을 web application을 위해서 사용할 필요는 없음
				- 스프링을 다룰 때 마우스, 모니터, 키보드 입력은 사용하지 않지만, 이미지나 폰트에 관련된 작업을 사용할 경우가 있다. 이 때 자바에서는 awt headless를 설정할 수 있기 때문에 해당 옵션을 통해서 설정할 수 있도록 만들어 둔 것 같다.
		- 여러 경로에 있는 properties 값들을 읽어서 저장한다.
			- profile, classpath 경로, servlet인지 webflux인지 등
		- 읽어들인 properties 정보를 spring appliction에서 사용할 수 있는 형태로 binding한다.
			- 객체로 바인딩함.
		- ApplicationContext를 초기화한다.
			- webflux, servlet 그리고 annotation, xml인지 구별해서 context를 생성함
		- ConversionService를 초기화한다.
			- ConversionService
				- @PathVariable, @RequestParam 등으로 넘어오는 값을 객체로 변환하는 서비스 제공
				- 실제 변환은 Converter가 담당하고 ConversionService는 일종의 묶음을 제공
					- String -> Integer, String -> Double 이런 여러 변환기를 묶음으로 제공

 - ApplicationListener, ApplicationPublisher는 무엇일까?
	 - 비동기적 동작을 위한 내부에서 이벤트를 발생시키는 것인가?
- generic type resolver
	- generic은 런타임에 object 혹은 extends 한 type으로 교체가 된다.
	- 이것은 type eraser라고 한다.
	- 그렇다면 런타임에 generic 타입을 알 수 있는 스프링은 어떤 원리로 이걸 가능하게 할까?
	- type
		- type token
			- Class< ? > type
				- Class 클래스는 JVM에서 구동되는 클래스를 나타낸다.
				- 클래스 리터럴 (String.class 이런 모양)을 전달하면 Class < String > type 이런 타입 정보를 알 수 가 있다.
				- 여기서 넘어오는 < ? > 에 들어오는 값을 타입토큰이라고 한다.
				- < T > T readValue(String content, Class< T > value Type) 이렇게 받으면 파라미터로 넘어온 클래스 리터럴을 바탕으로 타입 토큰을 인자로 받아서 리턴한다.
		- type token의 한계점
			```
			// 이건 가능함.
			Map<Class<?> type, Object> map = new HashMap<>();

			<T> void put(Class<T> key, T value) {
				map.put(key, value);
			}

			<T> T get(Class<T> clazz) {
				return clazz.cast(map.get(clazz));
			}


			map.put(String.class, "hello");
			map.put(Integer.class, 1);
			map.get(String.class);
			map.get(Integer.class);
			```
			```
			// 이렇게 generics로 저장된 정보는 삭제가 됨.
			map.put(List.class, Arrays.asList(1, 2, 3));

			// 런타임에는 타입 정보가 삭제된다.
			// List.class 상태로 가져올 수는 있어도 List<Integer>.class는 안 됨.
			map.get(List<Integer>.class);

			// 이렇게 따로 캐스팅할 필요가 있다.
			(List<Integer>)map.get(List.class);
			```
		- super type token
			- 위 List< Integer >.class가 가능하게 하는 방법
			- super 타입을 토큰으로 사용한다.
			- Class에서 제공하는 API와 ParameterizedType에서 제공하는 API로 generics의 정보를 알 수 있다.
				- 어떻게?
					- ClassRepository에 저장되어 있는 generics 정보를 가져올 수 있다.
					- 위 클래스는 리플렉션, JDI에서만 사용되도록 고안된 클래스 
					- 리플렉션은 런타임에 코드를 생성할 수 있는 기능이니 아마 그러한 런타임에 사라지는 generics 정보를 담기 위해서 존재하는 것 같다.
				- 기능을 지원하는 패키지가 sun.* 으로 시작함.
					- sun package는 자바 표준이 아니기 때문에 추후 문제가 될 수 있다.
					- sun은 오라클에서 사용함. 다른 벤더는 사용하지 않을 수 있음.
						- 자바의 장점이 한 번 쓰고 여기저기서 돌릴 수 있는 것인데 이 장점을 사용할 수 없게 된다.
					- 직접적으로 사용하지 말 것.

- SafeVarags?
	- parameterized type : 정해진 수가 아닌 여러개의 파라미터를 받을 수 있다.
	- generics를 사용해서 parameterized 타입을 사용할 수 있다.
	- 이 둘을 조합하면 문제가 생긴다.
		- heap polluation
			- 이건 뭘까?
			- parameterized type을 사용할 때, 객체가 가지고 있는 타입이 유지가 안 돼 발생하는 문제.
				- 항상 parameterized type에서만 발생하지 않는다. generics를 사용할 때 타입 정보를 제대로 알 지 못하면 발생하게 됨.
				```
				public class HeapPollutionDemo
				{
				    public static void main(String[] args)
				    {
				        Set s = new TreeSet<Integer>();
				        Set<String> ss = s; // unchecked warning
				        s.add(new Integer(42));  // another unchecked warning
				        Iterator<String> iter = ss.iterator();
				
				        while (iter.hasNext())
				        {
				            String str = iter.next();// ClassCastException thrown
				            System.out.println(str);
				        }
				    }
				}
				```
			- 일반적으로 컴파일할 때 감지가 되고 unchecked 경고가 출력됨.
			- 만약 런타임에 문제가 생긴다면 ClassCastException이 발생한다.
			- 왜 발생하는가?
				- type arguments, variables (genercis에서 쓰는 T같은 일반화된 매개변수 혹은 변수)는 런타임에 구체화되지 않는다.
					- 자바 5부터 generics를 지원하는데, 당시 하위호환을 고려해서 런타임에는 type arguuments, variables가 Object 혹은 Super로 지정된 타입으로 교체
				- 즉, 컴파일 타임에는 타입을 검사할 수 있는데 런타임에는 이걸 믿고 사용하면 문제가 발생할 수 있다.
				- varags를 사용할 경우, T[] 는 파라미터로 전달 받은 타입이 아닌, Object[]로 변환된다.
		- 컴파일러가 이걸 확인하기 어렵기 때문에 경고 메시지를 출력한다.
			- 이는 완벽할 수 없다. 만약 코드가 분리되어 서로 달리 컴파일 될 경우 문제가 발생할 수 있음.
				- 예) lib1.jar에서 사용하는 코드와 lib2.jar에서 사용하는 코드가 서로 분리되었을 경우
		- 이 경고 메시지를 없애기 위해서 안전하다는 것을 표현한다.

```
	public static <T> T[] unsafe(T... elements) { 
		return elements; 
		// unsafe! don't ever return a parameterized varargs array
	}
	 
	public static <T> T[] broken(T seed) { 
		T[] plant = unsafe(seed, seed, seed); 
		// broken! This will be an Object[] no matter what T is return plant; 
	} 
	
	public static void plant() { 
		String[] plants = broken("seed"); // ClassCastException 
	}
```

- 그래서 어떻게 이 문제를 해결하는가?
	- raw type과 parameterized type을 혼용해서 사용하지 말 것.
		- List list1 와 List< Integer > list2 를 혼용해서 사용하지 말 것
	- varags를 사용할 때는 주의할 것.
		- 만약 ClassCastException이 없다고 확신하면, SafeVargs를 사용하라

	- Json Parser는 generics 타입을 어떻게 보장할까?
		- super type token?
		- jackson의 경우
			- 코드를 분석해보니 Object로 다 담아서 저장한다.
			- 언제 Object를 -> generics로 바꾸는 거지?
			- 아니면 그럴 필요가 없는 것인가?
			- 어차피 하나 씩 따로 만들어서 collection에 저장한다.
				- collection에서 꺼낼 때는 문제가 없다. 왜냐하면 꺼낼 땐 타입이 상관 없으니까
				- 저장된 객체는 해당 타입에 맞춰 생성되어 넣어졌다.

- jackson의 궁금점
	- 상속 관계에서 프로퍼티는 어떻게 탐색?
	- getter, setter, default constructor가 없는 상태에서는 어떻게 직렬, 역직렬화?
	- 사용자가 만든 reference type은 json으로 전달될 때 어떻게 직렬, 역직렬화?
	- 일단 일반적인 과정
		- parser로 파싱
		- parsing된 정보와 클래스 리터럴을 바탕으로 역직렬화 생성
		- 직렬화하는 과정은 프로퍼티 값을 읽어 해당 레퍼런스 타입에 맞는 serializer를 찾아서 deserializing 진행
			- 해당 생성자를 부를 어노테이션과 프로퍼티 값을 알고 있으니 객체를 생성해서 value로 저장
			- 이후 key - value로 매핑
		- Object로 만들어진 객체를 캐스팅. 캐스팅은 전달된 클래스 리터럴을 통해서 캐스팅

- why jackson is faster than others?
	- 고려해볼 사항
		- protocol
			- 통신할 때 전달되는 속도가 프로토콜마다 다르다.
		- Jackson에서 사용하는 알고리즘
			- hikaricp 처럼 마이크로 최적화 기법이 사용되었을 수도
	- 직렬화, 역직렬화 과정에서 퍼포먼스 향상을 위해 고려해야 할 것은?

- jackson streaming api?
	- 만약 엄청 큰 json이 전달될 경우
		- 이걸 전부 받아서 한 번에 처리하는 것이 좋을까?
			- 전부 받아서 한 번에 처리하는 것은 메모리 사용에 부담이 될 수 있음.
		- 아니면 stream으로 받아서 부분 씩 처리하는 것이 좋을까
	- large scale json
		- 얼만큼 커야 large scale인가

- streaming 처리 기법
	- Jackson streaming api처럼 다른 곳에서 어떻게 사용?
	- 데이터 처리를 stream으로 처리하는 방법은?
		- http와 같은 프로토콜은 어떻게?
	- stream processing이란 무엇인가?
		- InputStream, Output Stream 이런 것과 event stream 이런 것의 차이는?
		- java stream api와 관련성은?
		- 어디서 사용이 될까
	- 내가 구현한다면 어떻게?
	- 이 기법 어디서 장점?
		- 계산 집약적인 경우
			- 왜?
		- 데이터 병렬화
		- 데이터 지역성
			- 하나 처리하고 다음 하나 받아서 하나 처리하는 곳. 처리한 데이터는 다시 읽지 않는다.
				- 영상, 이미지  

- 병렬 처리 기법이 있는데 왜 stream 처리를 사용할 필요가?
### References
---
[]()
https://sungminhong.github.io/spring/superTypeToken/
https://www.oracle.com/java/technologies/faq-sun-packages.html
https://en.wikipedia.org/wiki/Heap_pollution
https://docs.oracle.com/javase/tutorial/java/generics/nonReifiableVarargsType.html
https://gafter.blogspot.com/2006/12/super-type-tokens.html
https://github.com/FasterXML/jackson-docs/wiki/Presentation:-Jackson-Performance/304614265910651ef5aa4ca04280a81fde9f8c21j
https://noobtomaster.com/jackson/handling-large-json-documents-with-the-streaming-api/
https://homoefficio.github.io/2016/11/30/%ED%81%B4%EB%9E%98%EC%8A%A4-%EB%A6%AC%ED%84%B0%EB%9F%B4-%ED%83%80%EC%9E%85-%ED%86%A0%ED%81%B0-%EC%88%98%ED%8D%BC-%ED%83%80%EC%9E%85-%ED%86%A0%ED%81%B0/#about