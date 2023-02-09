# @Basic

`javax.persistence.Basic`

https://docs.jboss.org/hibernate/orm/5.5/userguide/html_single/Hibernate_User_Guide.html#basic-annotation

- 기본적으로 적용되는 요소

아래의 두 A 클래스는 궁극적으로 동일하다

```java
@Entity(name = "Product")
public class Product {
	private String name;
}

@Entity(name = "Product")
public class Product {
	@Basic
	private String name;
}
```

## 속성

**optional**

- `boolean` (default true)
- null을 허용하는지 여부를 정의

**fetch**

- FetchType (default EAGER)
- 속성에 대한 LAZY 여부 정의
- [bytecode enhancement](https://docs.jboss.org/hibernate/orm/5.5/userguide/html_single/Hibernate_User_Guide.html#BytecodeEnhancement)을 사용하는 경우에만 적용 가능. 그 외에는 무시된다.

## 속성 테스트

### optional = false

```java
@Entity(name = "Product")
public class Product {
	private String name;
}
```

null로 생성 시도 시, 아래의 

```
DataIntegrityViolationException: not-null property references a null or transient value
```