# [MySQL] lock

MySQL-innoDB 의 lock은 3종류가 있다.

1. Row-level lock
2. Recode lock
3. Gap lock

## 1) Row-level lock

테이블 row 마다 걸리는 lock

- Shared lock
- Exclusive lock

### Shared lock

- read에 대한 lock
- 일반적인 select 쿼리는 lock을 사용하지 않음
- 일부 select에서는 read 작업 수행 시 각 row 단위에 lock을 건다.

### Exclusive lock

- write에 대한 lock
- select for update, update, delete 시 row 단위에 lock

### lock을 거는 규칙

- 여러 트랜잭션이 동시에 한 row에 `Shared lock`을 걸 수 있다.
- 문제는 `Shared lock` 상태에서 다른 트랜잭션이 `Exclusive lock`을 시도할 수 없다.
	- 다른 트랜잭션이 읽은 row는 수정, 삭제 등 불가능
- `Exclusive lock`이 걸려있는 row에는 다른 트랜잭션이 `Shared lock`, `Exclusive lock`을 걸 수 없다.
	- 다른 트랜잭션이 수정하거나 삭제하고있는 row 는 읽기 수정 삭제가 모두 불가능


## 2) Recode lock

index record에 걸리는 lock

여기도 `Shared lock` 과 `Exclusive lock` 존재

- `select ~ for update`로 조회하여 lock을 거는 경우

```sql
select id, payment from sales where id = 10 for update;
```
위의 상태에서 10 에 대한 row lock (Exclusive lock)이 획득된 상태
이 상태에서 다른트랜잭션이 아래를 실행

```sql
update sales set payment = 20 where id = 10;
```

```sql
select payment from sales where id = 10;
```

둘 중 하나를 실행하려고하면 앞서 select ~ for update가 끝날 때 까지 대기한다.

위의 상황은 **동시성**을 우려하여 처리하는 경우가 있다.
처리량이 적을 때는 문제가 되지 않을 수 있지만, 처리량이 많아질수록 엄청난 성능 저하가 발생할 수 있다.


## 3) Gap lock

index record 의 gap에 걸리는 lock
**gap**: index 중 db에 실제 record가 없는 부분

예)

```sql
select id
from sales
where
	id between 1 and 10
for update;
```
DB에 id 가 1, 3, 8 들어가 있다고 가정해보자.
이 때 1~10 사이에 1,3,8이 아닌 2,4,5,6,7,9,10 으로 insert 시도할 때 gap lock 때문에 대기 상태에 걸린다.


## Dead lock

REPEATABLE-READ 상태에서의 dead lock