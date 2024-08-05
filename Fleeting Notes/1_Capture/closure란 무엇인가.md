created at : 2024-03-05 12:55

#### tags

#CS 

--- 

- lambda calculus에서 등장하는 개념
	- abstraction
		- 함수를 추상화하여 표현할 수 있다.
		- 모양만 보면 함수와 다를 바가 없다.
		- 람다 추상화는 정의만 있고 수행하지 않는다는 의미이다.
		- 람다를 표현하기 위해 사용된 변수는 속박된다.
	- free variable
		- 람다 추상화를 통해서 묶이지 않은 변수
	- 표현식으로 이 둘이 동시에 등장할 수 있다.
		- λx.x/y+2
		- x는 속박되었지만, y는 자유 변수
		- y는 아무거나 들어올 수 있지만, 조건이 없을 경우 연산이 불가능할 수 있음.
		- 이러한 y를 특정 조건 범위 안에 '가두는(close)' 또 다른 람다 표현식을 'closure'라고 한다.
		- closure가 주어지면 더 이상 y는 자유 변수가 아니다.
- 그렇다면 클로저, 람다 등 프로그래밍 언어에서 이걸 사용하는 이유? 왜 이걸 도입했는지?
	- 언어에서 사용하는 클로저의 개념이 람다 대수와 1:1 대응하는 경우도 있고 아닌 경우도 있다.
	- 언어에 따라 구현되는 모양도 다르다.
	- 사실상 거기에서만 통용되는 개념. 학문적인 개념과는 다르다.

### References
---
[between closure and lambda - stackoverflow](https://stackoverflow.com/questions/220658/what-is-the-difference-between-a-closure-and-a-lambda)
[java 8 lambda capturing, free variables - blog](https://bugoverdose.github.io/development/lambda-capturing-and-free-variable/)
