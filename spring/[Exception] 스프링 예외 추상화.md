# [Exception] 스프링 예외 추상화 - DB 접근

스프링이 제공하는 예외 추상화에 대해 알아보기전에 Java의 Checked Exeption으로 시작해보자.

## 배경1) Checked Exception

`Exception` 과 그 하위 예외 중 **`RuntimeException`을 제외한 모든 예외**는 Checked Exception이다.

예외는 catch 하거나 밖으로 던지도록 선언해야 한다.

### 장점

꼭 예외를 처리하도록 강제화한 도구이다.
개발자가 실수로 예외를 누락하지 않도록 컴파일러를 통해 문제를 잡아주는 안전장치이다.

### 단점

강제화된 만큼 번거로운 일이 된다.
크게 신경쓰지 않고 넘어갈 수 없다.
복구 불가능한 예외
의존관계에 따른 단점도 발생한다.

### 복구 불가능한 예외

SQLException을 예로 DB에 문제가 생겨서 발생하는 예외이다.
문법, DB자체 등 뭔가 문제가 발생한 것이다.
이러한 문제들은 서비스나 컨트롤러가 해결할 수 없다.
일관성있게 공통으로 처리해야 함.
서블릿 필터, 스프링 인터셉터, 스프링 ControllerAdvice를 사용하면 깔끔하게 공통으로 해결할 수 있다.


### 의존관계 문제

`SQLException`과 `ConnectionException`은 Checked Exception이다.

Repository에서 `SQLException`이 발생하여 예외를 던지면 해당 예외는 Service에서 처리해야 한다.
NetworkClient 에서는 `ConnectionException`이 발생하여 예외를 던지면 또 Service에서 처리해야 한다.

**하지만, Service 입장에선 DB나 Network 문제를 처리할 수 없다.** 결국 밖으로 던진다.

받은 Controller 입장에서도 처리할 수 없다.

### 의존관계에 의한 파급 효과

이런 상황에서 DB기술을 향후 JDBC 기술이 아닌 다른 기술로 변경되면 어떻게 될까?
JPA로 변경되어 SQLException이 아닌 JPAException이 발생하게 되면 어떻게 될까?

Repository, Service, Controller 모두 의존성을 바꿔줘야한다.

## 배경2) Unchekced Exception 활용

문제가 발생한 지점에서 밖으로 chekced exception 을 던지지 않고 unchecked exception으로 전환시켜서 던진다.

```java
try {
    // SQLException를 강제화하는 DB 접근 코드
} catch (SQLException e) {
    throw new MyDbException(e);
}
```

RuntimeException자체로 던지면 받는 쪽에서 세부적인 에러 처리가 모호해질 수 있으니 명확하게 DB에러를 위한 MyDbException을 만들어서 활용한다.

```java
 catch (SQLException e) {
    // h2 db
    if (e.getErrorCode() == 23505) {
        throw new MyDuplicateKeyException(e);
    }
    throw new MyDbException(e);
}
```

또한 키 중복인 상황을 대처하기 위해 별도의 MyDbException를 상속한 MyDuplicateKeyException를 만들어서 예외로 던져줄 수 있다.

### 하지만 너무 많은 DB 예외 케이스

비즈니스 구현이 케이스마다 다양하고 별도로 생성해줘야하는 예외 케이스들이 너무 많다. 또한 DB마다 에러 코드도 제각각이라 관리하는 것이 거의 불가능에 가깝다.


## 스프링의 예외 추상화

앞의 문제를 해결하기 위해 예외를 추상화해서 제공해준다.

DB의 예외 추상화 최상위 클래스는 `DataAcessException`

예를 들어 문법오류에 의한 Exception은 `BadSqlGrammarException`가 발생하고 이 클래스는 `DataAcessException` 하위 클래스이다.

하지만, 이 또한 일일히 SQLException에서 발생하는 경우를 모두 찾아서 변환해주는 것이 비현실적이다.

### SQL 예외 변환기

SQLExceptionTranslator 를 통해 변환된 에러를 반환받을 수 있다.

```java
public class MemberRepositoryImpl implements MemberRepository {

    private final DataSource dataSource;
    private final SQLExceptionTranslator exTranslator;

    public MemberRepositoryImpl(DataSource dataSource) {
        this.dataSource = dataSource;
        this.exTranslator = new SQLErrorCodeSQLExceptionTranslator(dataSource);
    }
    
    @Override
    public Member save(Member member) {
    	// 생략
    	try {
            // 생략
        } catch (SQLException e) {
            throw exTranslator.translate("save 에러", sql, e);
        }
    }
}
```
SQLExceptionTranslator 인터페이스 구현체 여러개 중 에러코드 기반으로 변환해주는 SQLErrorCodeSQLExceptionTranslator로 구현해본다.
스프링 내부에 DB별로 제각각인 에러 코드도 정의하고 있다. 


## 참고

- [[인프런] 스프링 DB 1편 - 데이터 접근 핵심 원리](https://www.inflearn.com/course/%EC%8A%A4%ED%94%84%EB%A7%81-db-1/dashboard)