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

### 커넥션 풀

- 커넥션의 획득 과정
  - 커넥션 조회
  - TCP/IP
  - ID, PW 전달
  - 커넥션 생성 완료
- 커넥션을 필요할 때마다 생성하는 것은 비효율적
- 미리 커넥션을 생성해 두고 재사용
- 커넥션 풀에서 커넥션을 가져다 사용
- hikariPool 뜨는게 기본적인 커넥션 풀
- Pool 생성은 별도의 스레드에서 동작

### DataSource

- DriverManager -> 커넥션 생성
- HikariCP -> 커넥션 풀
- 커넥션 획득 방법 변경 시 코드 수정 필요
- DataSource를 통해 커넥션 획득 방법을 추상화

```java
public interface DataSource {
  Connection getConnection() throws SQLException;
}
```

### 스프링과 문제 해결

일반적인 앱 구조

> `프리젠테이션 계층` -> `서비스 계층` -> `데이터 접근 계층`

- 서비스 계층은 가급적 특정 기술에 의존하지 않고 자바 코드로 작성

#### 순수한 서비스 계층

- 핵심 비지니스 로직
- `UI`나 `DB`가 변경되어도 서비스 계층의 코드는 변하면 안된다.

#### 트랜잭션 추상화

- `Service`가 인터페이스에 의존
- 스프링에서 `PlatformTransactionManager`를 제공
