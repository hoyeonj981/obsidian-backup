created at : 2024-03-05 12:54

#### tags

#tools

--- 

- 코어 컨셉
	- settings.gradle과 단일, 여러 모듈에 있는 build.gradle를 읽어서 빌딩 task 과정에 따라 빌드를 해서 jar, war, apk 등 실행 가능한 프로그램으로 만들어 주는 툴
- gradle 빌드 방법
	- gradle를 직접 설치해서 빌드
	- gradlew (gradle wrapper)를 통해서 빌드 - 권장하는 방법.
- gradlew는 선언된 gradle를 호출하는 스크립트. 필요에 따라 미리 다운로드할 수 있음.
	- 장점
		- gradle 버전을 표준화할 수 있음
		- 다른 사용자에게도 동일한 gradle 버전 제공
		- 다른 환경에서도 동일한 gradle 버전 제공

### References
---
[gradle user guide(core) - docs](https://docs.gradle.org/current/userguide/gradle_basics.html#gradle)
