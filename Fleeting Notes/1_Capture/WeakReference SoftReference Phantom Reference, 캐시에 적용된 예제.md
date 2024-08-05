created at : 2024-03-18 16:44

#### tags

#Java #CS #GC

--- 

- 왜 이런 개념이 필요한가?
	- GC가 될 레퍼런스에 대해서 관여하고 싶을 때 사용
- SoftReference
	- OutOfMemoryError 이전까지는 남겨 두다가 GC가 발생하는 레퍼런스
	- 마지막으로 참조된 객체로 부터 얼마의 시간동안 생존할 지 결정할 수 있다.
	- 1초 / (남은 여유 힙 공간)으로 남은 시간을 계산해서 생존함.
	- XX:SoftRefLRUPolicyMSPerMB 를 통해서 조절이 가능하다.
	- 캐시로 활용할 수 있다.
- WeakReference
	- strong과 soft의 중간 상태의 레퍼런스
	- 오로지 weak reference를 통해서만 접근할 수 있다.
	- 왜 굳이 필요한가?
		- soft reference는 referent가 마지막으로 참조된 이후에 사용될 수 있다.
		- weak reference는 referent가 살아만 있다면 접근이 가능하다.
- phantom reference
	- strong, soft, weak도 아닌 참조
	- gc에 의해 처리가 결정되었지만 아직 처리가 안 된 상태.
	- 처리가 안 된 상태라면 phantom reference를 통해서 접근이 가능하다.

### References
---
[]()
https://www.baeldung.com/java-soft-references
https://www.baeldung.com/java-weak-reference