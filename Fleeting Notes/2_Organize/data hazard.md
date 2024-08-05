created at : 2024-02-14 20:42

#### tags

#CS #Processor 

--- 

- hazard
	- 파이프라이닝에서 현 실행 사이클에 다음 명령어를 처리할 수 없는 상태.
	- pipeline stall, pipeline bubbling, operand forwarding
	- 비순차적 실행, scoreboarding method, tomasulo algorithm
- data hazard
	- 다른 파이프라인 단계에서 데이터가 서로 연관되어 있는 상태. race condition을 발생 시킬 수도 있다.
	- WAR (write after read)
			```
			i1. R4 <- R1 + R5
			i2. R5 <- R1 + R2
			```
		- r5가 읽어진 후에 쓰기 작업이 진행된다. (예상)
		- 병렬 실행 중에 r5가 쓰여진 후에 읽기 작업이 진행될 수 있다.
	- WAW (write after write)
			```
			i1. R5 <- R4 + R7
			i2. R5 <- R1 + R3
			```
		- i2의 write back 작업이 i1에 의해 딜레이될 수 있다.
	- RAW (read after write)
			```
			i1. R2 <- R5 + R8
			i2. R4 <- R2 + R8
			```
		- r2가 쓰인 다음에 읽기가 바로 진행되어야 한다. (예상하는 결과)
		- 파이프 라이닝 중에 r2가 저장이 안된 상태에서 r4가 연산될 수 있다.
- data hazard 방지
	- pipeline bubbling - 파이프라인에 딜레이 추가하기
	- 비순차적 실행 - 파이프라인 버블을 최소화하기 위해서 비순차적 실행
### References
---
[hazard(computer science) - wiki](https://en.wikipedia.org/wiki/Hazard_(computer_architecture)#Data_hazards)

