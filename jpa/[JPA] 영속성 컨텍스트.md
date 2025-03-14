# [JPA] 영속성 컨텍스트

"엔티티를 영구 저장하는 환경"

논리적인 개념이다.


persist();

- 영속성 컨텍스트에 저장한다.
-  영속시킨다.

## 1차 캐시

영속성 컨텍스트 내부에는 1차 캐시 공간이 있다.

1차캐시 공간에
"member1"만 있다고 가정해보자.

find("member1");

먼저 1차 캐시 공간을 찾는다.
캐시 공간에 있으면 캐시에서 찾아서 리턴.

find("member2");

1차 캐시 공간에 없다.
디비에서 조회에서 캐시공간에 저장 후 반환한다.

이 영속성 컨텍스트 (1차캐시)는 하나의 트랜잭션 단위로 저장하기 때문에 하나의 트랜잭션 내에서 유효하다.

```java
Member member1 = em.find(Member.class, 1L);
Member member2 = em.find(Member.class, 1L);
```

위의 두번의 find는 실제로 쿼리가 1개 날아간다.

## 쓰기 지연 SQL 저장소

persist() 했을 때 실제 쿼리가 날아가는 것이 아닌 Transction commit이 될 때 한번에 쿼리를 보낸다. 그 때 까지 쓰기 지연 SQL 저장소에 보관하고 있는다.

persist(member)
persist(member)

두 번 했을 때 쿼리가 하나 날아간다. 동일 객체를 인지함.

하이버네이트는 batch_size 설정하여 설정한 개수만큼 모아서 한번에 쿼리를 보낼 수 있다.

### 더티 체킹

= 변경 감지

영속된 객체를 수정하면 영속성 컨텍스트가 변경을 감지하기때문에 transaction.commit() 해줄 필요가 없다.

**스냅샷**

- 조회해오면 조회해온 객체를 스냅샷 찍어놓는다.
- 객체의 값을 변경하면 커밋 시점에 (내부적으로 flush() 발생할 때) 변경사항을 체크한 후 변경과 관련된 SQL 쿼리를 날린다.
- 수정, 삭제 모두 해당

**영속된 객체가 변경될 때는 persist, update 하는 로직을 작성하지 말자.**

동작상의 문제는 없다. 하지만 이해도가 낮은 상태로 코드를 작성하다보면 혼란을 야기할 수 있는 부분이 있다.


## 플러시

영속성 컨텍스트의 변경 내용을 데이터베이스에 반영.
반영한다고 1차캐시의 내용이 사라지는 것은 아니다.
단지 데이터베이스에 반영할 뿐이다.

### JPQL 쿼리 실행 시 플러시가 자동으로 호출되는 이유

em.persist(memberA);
em.persist(memberB);
em.persist(memberC);

em.createQuery("select m from Member m", Member.class);
List<Member> members = query.getResultList();

persist에 대한 내용을 영속성 컨텍스트가 가지고 있는 상태에서, SELECT 해오면 persist한 데이터는 조회되지 않는다.
JPA는 이런 문제를 방지하기 위해 JPQL 기본모드가 실행될때 flush 되도록 해놨다.
(persist한 객체가 아닌 다른 데이터를 SELECT 하려고 해도 flush가 동작한다. 그래서 모드를 바꿀 수 있지만 권장하지는 않는다.)

### 플러시는

- 영속성 컨텍스트를 비우지 않는다.
- 영속성 컨텍스트의 변경내용을 데이터베이스에 반영한다.
- **트랜잭션**이라는 작업 단위가 중요. 어 커밋 직전에만 동기화 하면 됨

## 준영속

1차 캐시에 올라가 있는 상태를 '영속'상태라고 한다.
영속성 컨텍스트에서 분리시키는 것이 '준영속'

플러시될 때 '영속'상태만 반영
