# Test 관련 여러가지 학습

> 테스트 도구, 테스트 방법, 테스트에 관한 가치관 등 테스트 관련 여러가지 기록을 합니다.


## @SpringBootTest를 사용한 통합테스트 너무 오래걸린다.

왜일까?

일단 관련 이슈를 찾음

[[spring-boot github issue] Context is reloading when using @MockBean #10015](https://github.com/spring-projects/spring-boot/issues/10015)


- TestContext Framework 기반으로 동작함
- ApplicationContext 를 캐시함
- 캐시는 하는데 mockBean 이 달라지면 ApplicationContext 구성이 바뀜
- 즉, 동일한 컨텍스트를 캐시할 수 없으니 매번 다시 실행한다고 봐야함


참고자료 
- [spring framework - testcontext framework](https://docs.spring.io/spring-framework/reference/testing/testcontext-framework.html)
- [[spring-boot github issue] Context is reloading when using @MockBean #10015](https://github.com/spring-projects/spring-boot/issues/10015)
- [[medium] Spring Boot Test에서 Mockito를 활용한 OOM 및 테스트 속도 최적화 방법](https://medium.com/@itsinil/spring-boot-test-%EC%97%90%EC%84%9C-mockito-%EC%82%AC%EC%9A%A9-%EC%8B%9C-oom-%EB%B0%8F-%ED%85%8C%EC%8A%A4%ED%8A%B8-%EC%86%8D%EB%8F%84-%EC%A0%80%ED%95%98-%ED%96%A5%EC%83%81%EC%8B%9C%ED%82%A4%EA%B8%B0-6b7649ec646e)

