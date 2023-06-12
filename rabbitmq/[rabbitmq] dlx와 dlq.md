# [rabbitmq] dlx와 dlq

공식문서 보고 정리하면서 공부하기

- https://www.rabbitmq.com/dlx.html

## DLX (Dead Letter Exchanges)

- 컨슈머에서 메시지를 부정 처리한 경우(`basic.reject` 혹은 `basic.nack` 를 사용하면서 `requeue=false` 로 전달)
- 메시지가 TTL 설정으로 만료된 경우
- 큐의 길이 제한을 초과하여 메시지가 삭제되는 경우
(그러나, 큐의 만료는 그 안의 메시지를 데드 레터하지 않음.)

### DLX 정의
- 큐 아규먼트
- **(권장)** 정책 
    - 이는 애플리케이션의 재배포를 수반하지 않고 DLX를 재구성할 수 있게 해줍니다.

큐 아규먼트로의 설정이 우선순위 높음. (큐 아규먼트, 정책 둘 다 정의되어있을 경우 큐 아규먼트가 정책을 덮어씌움)
