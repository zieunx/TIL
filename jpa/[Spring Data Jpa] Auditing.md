# [Spring Data Jpa] Auditing

### 참고

- https://docs.spring.io/spring-data/jpa/docs/2.7.x/reference/html/#auditing

> 2.7.x 버전 기준

## Auditing

**엔터티를 생성하거나 변경한 사람과 변경이 발생한 시기를 추적**할 수 있는 기능을 제공한다.
이 기능을 사용하려면,
- Auditing 기능을 활성화 해줘야한다. (어노테이션 설정 or XML설정)
- 엔티티 클래스에 어노테이션 혹은 인터페이스 구현을 해줘야 한다.

Spring Data Jpa는,
- Spring Data JPA 1.5 부터는 Auditing 활성화를 위해 @EnableJpaAuditing 를 configuration 클래스에 달아주면 된다.
- 엔티티 클래스에 AuditingEntityListener를 등록해줘야 한다.


## metadata

다음의 네 가지 정보를 제공해준다.

- 등록일자 `@CreatedDate`
- 등록자 `@CreatedBy`
- 수정일자 `@LastModifiedDate`
- 수정자 `@LastModifiedBy`

### `@CreatedDate`, `@LastModifiedDate`

날짜를 나타내는 두 어노테이션은,
- `DateTime`
- JDK8 date and time Type
- `long` / `Long`
- (legacy) `Date`, `Calendar`

유형으로 사용 가능하다.

### `@CreatedBy`, `@LastModifiedBy`

`@GreatedBy`, `@LastModifiedBy` 어노테이션은 현재 사용자가 누구인지 알아야 한다.
해당 구현은 아래의 링크로 참조하면 되겠다.

아래의 링크에서는 Spring Security 의 Authentication을 활용한 예제를 보여준다.

https://docs.spring.io/spring-data/jpa/docs/2.7.x/reference/html/#auditing.auditor-aware


## 사용하기

`persistence` 의존성이 아닌 `Spring Data Jpa` 의존성을 사용해야 한다.
`Spring Data Jpa` 의존성을 추가해준다.

> gradle 설정

```gradle
implementation 'org.springframework.boot:spring-boot-starter-data-jpa'
```


아래의 어노테이션 설정을 해준다.
```java
import org.springframework.data.jpa.repository.config.EnableJpaAuditing;

@EnableJpaAuditing
@SpringBootApplication
public class CategoryApplication {

	public static void main(String[] args) {
		SpringApplication.run(CategoryApplication.class, args);
	}

}
```

엔티티에서는 다음의 어노테이션들을 사용해주면 된다.

```java
class Customer {
  @CreatedBy
  private User user;

  @CreatedDate
  private Instant createdDate;

  // … further properties omitted
}
```

임베디드 엔티티로도 가능하다.

```java
class Customer {

  private AuditMetadata auditingMetadata;

  // … further properties omitted
}

class AuditMetadata {

  @CreatedBy
  private User user;

  @CreatedDate
  private Instant createdDate;

}
```