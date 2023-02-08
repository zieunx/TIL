slf4j# slf4j

slf4j: Simple Logging Facade for Java

풀네임을 통해 알수 있듯, `파사드 패턴`을 사용한 Java 로깅 API이다.
여러 로깅 프레임워크의 추상화 역할.

### Facade Pattern

복잡한 서브 시스템을 하나의 메인 시스템으로 제공하는 패턴.

### Facade

`org.slf4j.Logger`가 Facade 추상화 인터페이스

### 구현
`org.slf4j.slf4j-api`로 추상화 인터페이스를 사용할 수 있다.

구현 예시이다.
```java
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;

public class A {
	Logger log = LoggerFactory.getLogger(A.class);
    
    public void logging() {
    	log.info("A 로깅");
    }
}
```

구현체가 바뀌어도 코드에 지장이 없도록 위와 같은 추상화를 이용하는 방법이 좋아보인다.

하지만 구현체를 설정하지 않으면 구현체가 없기 때문에 정상적으로 로깅되지 않는다.
slf4j 바인딩은 여러가지 사용할 수 있다. 대표적으로 `logback` 사용.


### Spring Boot의 Logging

https://docs.spring.io/spring-boot/docs/2.7.8/reference/html/features.html#features.logging

> By default, if you use the “Starters”, Logback is used for logging.

starter 를 사용하면 기본적으로 `Logback`.


## 참고
- https://www.slf4j.org/manual.html
- https://docs.spring.io/spring-boot/docs/2.7.8/reference/html/features.html#features.logging