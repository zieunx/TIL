# [Transction] 트랜잭션 격리수준

- READ UNCOMMITTED
- READ COMMITED
    - 커밋된 읽기. 같은 트랜잭션내에서 변경된 후 조회하면 변경 전(커밋이 끝난)데이터를 읽음
- REPEATABLE READ(MySQL default)
    - 커밋된 읽기 + 같은 트랜잭션 내 변경에대한 조회는 동일성 보장
    - B읽음 → A 인서트 커밋 → B읽음 = B입장에서 두번의 조회가 다르다.(유령 레코드)
- SERIALIZABLE
    - 한 트랜잭션에서 사용하는 데이터를 다른 트랜잭션이 접근 불가하도록 설정