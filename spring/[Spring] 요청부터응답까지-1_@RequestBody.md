# 요청부터응답까지 - @RequestBody

## 참고
- [Spring 공식 문서 - @RequestBody](https://docs.spring.io/spring-framework/docs/current/reference/html/web.html#mvc-ann-requestbody)

## @RequestBody 공식 문서 살펴보기

- @RequestBody를 사용하여 요청 본문을 읽고 `HttpMessageConverter` 를 통해 객체로 역직렬화한다.
- `jakarta.validation.Valid` 혹은 스프링의 `@Validated` 어노테이션과 함께 사용할 수 있다. (둘 다 표준 Bean 유효성 검사 적용)
- 유효성 검사 오류는 기본적으로 MethodArgumentNotValidException 를 발생시킨다.

요청을 `HttpMessageConverter` 를 통해 객체로 역직렬화 한다고 한다.

그럼 `HttpMessageConverter` 를 알아보자.
