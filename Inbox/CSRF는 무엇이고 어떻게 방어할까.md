created at : 2024-02-24 13:14

#### tags

#CS #Network #Security

--- 

- cookie
- same origin policy
- 이해한것
	- 일단 sessionid 생성
	- sessionid를 가지고 위조된 요청이 전달됨
	- 그걸 바탕으로 의도하지 않은 행동 수행
- 그렇다면 어떻게 방어? 내가 직접 이걸 구현할 경우 어떻게?
	- referer 헤더 체크
		- host와 referer가 일치하는지
	- csrf token?
		- 서버에서 csrf token을 생성하여 클라이언트에게 함께 전달한다.
		- 클라이언트는 요청을 보낼때 항상 이 토큰을 같이 포함해서 전달한다. (ex - input hidden으로)
		- 조작된 요청을 보낼 때 해당 token도 같이 보내야 하는데 이것이 없다면 요청을 거부한다.
	 - 근데 토큰이 탈취되면 결국 무용지물 아닌가?
		 - 토큰은 어떻게 지킬 수 있는 것일까?

### References
---
[cross-site request forgery(csrf) explained](https://www.youtube.com/watch?v=eWEgUcHPle0)
