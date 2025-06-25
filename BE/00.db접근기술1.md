# DB 접근 기술

### 데이터 저장 과정

클라이언트 -> 서버 -> DB

- 동일한 과정이나 DB 벤더사에 따라 접근 방법이 달랐음
  - DB가 바뀌면 코드를 수정해야 함
- JDBC 인터페이스를 도입, 동일한 접근방법, 내부 구현을 다르게
  - 코드 수정이 필요 없음
- ORM(JPA)
  - JDBC로 많은 부분이 추상화
  - SQL의 경우 ANSI SQL 이외 다른 부분이 존재
  - 객체 모델링을 통해 SQL문을 직접 작성할 필요가 없음

### JDBC

JDBC를 편하게 사용하자

- SQL Mapper

  - JdbcTemplate
  - MyBatis
  - SQL을 직접 작성

- ORM
  - JPA
  - SQL을 동적으로 생성
