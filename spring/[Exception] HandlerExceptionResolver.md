# HandlerExceptionResolver

- 오류에 대한 Handler 매핑
- 실행 중에 발생한 예외를 어떤 Handler 로 처리할 것 인지에 대한 정보 제공

## Spring Boot 가 제공하는 3가지 ExceptionResolver

(우선순위순)

- ExceptionHandlerExceptionResolver
- ResponseStatusExceptionResolover
- DefaultHandlerExceptionResolver

위의 ExceptionResolver 를 역순으로 알아보자.

### DefaultHandlerExceptionResolver

스프링 내부에서 발생하는 스프링 예외를 처리  
ex) 파라미터 바인딩 시점에 타입이 안맞으면 내부에서 TypeMismatchException 발생  
    TypeMismatchException 은 `500` 에러. but, 잘못된 요청에 대한 HTTP 상태는 400.  
    500 -> 400 으로 변경 처리

### ResponseStatusExceptionResolover

1) @ResponseStatus 가 달린 예외  
2) ResponseStatusException 예외

### ExceptionHandlerExceptionResolver

- API 오류 응답의 경우 response에 데이터를 넣어야해서 번거롭고 ModelAndView 를 반환해야 하는 것도 API에 잘 맞지 않다.
- 위의 문제를 해결해주는 @ExceptionHandler
- 어노테이션이 붙은 메서드를 통해 예외를 처리할 수 있도록 하는 클래스
