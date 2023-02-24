# [Transction] 트랜잭션 격리수준

> [MySQL 공식문서](https://dev.mysql.com/doc/refman/8.0/en/innodb-transaction-isolation-levels.html) 보고 정리하기

[**일관된 읽기** 자세히 보기](https://dev.mysql.com/doc/refman/8.0/en/innodb-consistent-read.html)

**격리 수준**

1. READ UNCOMMITTED
1. READ COMMITED
    - 커밋된 읽기. 같은 트랜잭션내에서 변경된 후 조회하면 변경 전(커밋이 끝난)데이터를 읽음
1. REPEATABLE READ(MySQL default)
    - 커밋된 읽기 + 같은 트랜잭션 내 변경에대한 조회는 동일성 보장
    - B읽음 → A 인서트 커밋 → B읽음 = B입장에서 두번의 조회가 다르다.(유령 레코드)
1. SERIALIZABLE
    - 한 트랜잭션에서 사용하는 데이터를 다른 트랜잭션이 접근 불가하도록 설정



→ 데이터 격리 정도가 높아지며, 동시성도 떨어지는 순서

### 3가지 부정합 문제점 (동시성 부작용)

1. DIRTY READ
2. NON-REPEATABLE READ
3. PHANTOM READ

([참고](https://kth990303.tistory.com/313))

### **REPEATABLE READ**

- Mysql, MariaDB 의 기본 격리 수준
- 같은 트랜잭션 내에서의 [일관된 읽기](https://dev.mysql.com/doc/refman/8.0/en/glossary.html#glos_consistent_read) 는 첫번째 읽기에 의해 스냅샷이 저장된다.
- 같은 트랜잭션 내의 여러(nonlocking) ``SELECT``문은 다른 트랜잭션에 의해 데이터가 변경되더라도 같은 결과값이 나온다.
- 읽기잠금(locking reads)(SELECT with FOR UPDATE or FORE SHARE), UPDATE, DELETE 에 대해 아래의 두 가지 조건에 따라 다름
    - 명령문이 고유 검색조건이 있는 고유 인덱스인지
        
        → 발견된 인덱스 레코드만 잠그고 , not the gap before it.
        
    - 범위 유형 검색 조건을 사용하는지
        
        → gap locks 혹은 next-key locks 를 사용하여, 스캔된 인덱스 범위를 잠근다.
        
        범위에 포함된 부분을 다른 세션이 간섭하지 못하게 차단한다.
        

### READ COMMITTED

- 오라클에서 기본적으로 사용되는 격리 수준
- 같은 트랜잭션 내의 `일관된 읽기` 에서도 각각의 일관된 읽기는 자체의 새로운 스냅샷 생성
- 읽기잠금(locking reads)(SELECT with FOR UPDATE or FORE SHARE), UPDATE, DELETE 에 대해
    - InnoDB는 인덱스 레코드만 잠그고, 인덱스 레코드 앞에 있는 갭은 잠그지 않는다.
    - 따라서 새로운 레코드를 잠겨 있는 레코드 다음에 자유롭게 삽입하는 것을 허용한다.
    - 간격 잠금이 비활성화 되어있기 때문에, 다른 세션이 간격에 새 행을 삽입할 수 있으므로 [가상행](https://dev.mysql.com/doc/refman/8.0/en/innodb-next-key-locking.html) 문제가 발생할 수 있다.
    - 유니크 검색 조건과 함께 유니크 인덱스를 사용하는 UPATE 및 DELETE 명령문은 찾게되는 인덱스만을 잠그고, 인덱스 앞에 있는 갭을 잠그지는 않는다.
    - 
    

(진행중..)

### READ UNCOMMITTED

- 다른 트랜잭션에서 커밋되지 않은 데이터들을 읽어올 수 있다.
- COMMIT 이 성공적으로 된다면 문제가 없지만, COMMIT 되지 않고 ROLLBACK 된다면 잘못된 데이터를 읽어오게 되는 것이다.
- Dirty Read : 신뢰할 수 없는 데이터를 읽어오는 것
- ([출처](https://jupiny.com/2018/11/30/mysql-transaction-isolation-levels/#readuncommitted))