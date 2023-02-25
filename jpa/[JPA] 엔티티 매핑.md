# [JPA] 엔티티 매핑

## @Entity

@Entity를 사용해야 JPA 엔티티로 인지된다. 필수사항

### 주의
- 기본 생성자 필수 (public/protected)
- final 클래스, enum, interface, inner 클래스 사용불가
- 저장할 필드에 final 불가


