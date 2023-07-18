> 공식 문서 보고 정리하기

https://www.rabbitmq.com/ttl.html

메시지와 대기열 둘 다 TTL을 설정할 수 있다. 만료된 메시지와 대기열은 삭제된다.   
TTL은 Queue Arguement 에 의해 컨트롤되고 '정책'을 사용하는 방법이 가장 좋다.  
단일 대기열, 대기열 그룹, 메시지별로 적용할 수 있다.

### 정책

`message-ttl` 인수를 설정하거나 대기열 선언 시 동일한 인수를 지정하여 주어진 대기열에 대해 설정할 수 있다.

`message-ttl`: 메시지에 설정하는 ttl 속성. 해당 메시지의 최대 ttl이다.
`x-message-ttl`: 큐에 설정하는 ttl 속성. 해당 큐의 모든 메시지에 일괄적으로 적용된다. 해당 큐의 모든 메시지의 최대 ttl 이다.

- 만약 `message-ttl`가 `2초`고 `x-message-ttl`가 `4초`라면?
	-  `message-ttl`를 더 높은 우선순위로 `2초` 후에 사라진다.
- 만약 `message-ttl`가 2초고 `x-message-ttl`가 1초라면?
	- 큐의 최대 ttl인 `x-message-ttl`값을 우선순위로 1초 후에 사라진다.

