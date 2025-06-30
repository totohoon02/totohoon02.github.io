# JPA ID 생성 전략

JPA에서 ID 생성은 크게 **직접 할당**과 **자동 할당**으로 나뉩니다.

- 직접 할당: `@Id`만 사용해 개발자가 직접 ID를 지정
- 자동 할당: `@Id`와 `@GeneratedValue`를 함께 사용해 JPA가 자동으로 ID를 생성

---

## @GeneratedValue 어노테이션 구조

```java
@Target({ElementType.METHOD, ElementType.FIELD})
@Retention(RetentionPolicy.RUNTIME)
public @interface GeneratedValue {
    GenerationType strategy() default GenerationType.AUTO;
    String generator() default "";
}
```

- `strategy`: 키 생성 전략 설정 (기본값 `AUTO`)
- `generator`: 키 생성기 이름 (사용자 지정 시)

---

## GenerationType 종류와 설명

| 전략     | 설명                                                       | 특징 및 주의사항                                                                                                                   |
| -------- | ---------------------------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------- |
| AUTO     | DB 방언에 따라 IDENTITY, SEQUENCE, TABLE 중 자동 선택      | DB 변경 시 코드 수정 불필요                                                                                                        |
| IDENTITY | DB에 키 생성 위임 (MySQL, PostgreSQL, SQL Server 등)       | - INSERT 시 즉시 쿼리 실행<br>- Hibernate는 `getGeneratedKeys` 사용<br>- 배치 인서트 불가                                          |
| SEQUENCE | DB 시퀀스 객체 사용 (Oracle, PostgreSQL 등 시퀀스 지원 DB) | - `@SequenceGenerator`로 시퀀스 지정 가능<br>- `persist()` 호출 시 시퀀스에서 ID 조회 후 할당<br>- 쓰기 지연 가능 (커밋 시 INSERT) |
| TABLE    | 키 생성 전용 테이블을 만들어 시퀀스 흉내냄                 | - `@TableGenerator`로 테이블 지정 가능<br>- SELECT 및 UPDATE 쿼리 사용<br>- SEQUENCE 대비 성능 낮음<br>- 모든 DB 지원              |

---

## 전략별 동작 흐름

### IDENTITY

- 엔티티 저장 시 DB가 키 생성 → 즉시 INSERT 실행
- 식별자 할당 위해 즉시 DB 저장 필요 → 쓰기 지연 불가
- Hibernate 내부적으로 `Statement.getGeneratedKeys` 사용
- 배치 인서트 불가

### SEQUENCE

- `em.persist()` 호출 시 DB 시퀀스에서 키 미리 조회
- 조회된 키를 엔티티에 할당 후 영속성 컨텍스트에 저장
- 트랜잭션 커밋 시 INSERT 실행 → 쓰기 지연 가능
- 배치 인서트 가능

### TABLE

- 별도 키 생성용 테이블에서 현재 값 SELECT 후 UPDATE로 증가 처리
- SEQUENCE 대비 DB 통신 1회 더 발생 → 성능 저하
- 모든 DB에서 지원 가능

### AUTO

- DB 방언에 따라 IDENTITY, SEQUENCE, TABLE 중 적절한 전략 자동 선택
- DB 변경 시 코드 수정 불필요

---

## 예시 코드

### SEQUENCE 전략

```java
@SequenceGenerator(
    name = "member_seq_gen",
    sequenceName = "member_seq",
    initialValue = 1,
    allocationSize = 50
)
@GeneratedValue(strategy = GenerationType.SEQUENCE, generator = "member_seq_gen")
@Id
private Long id;
```

### TABLE 전략

```java
@TableGenerator(
    name = "member_table_gen",
    table = "id_generator",
    pkColumnName = "gen_name",
    valueColumnName = "gen_value",
    pkColumnValue = "member_id",
    initialValue = 0,
    allocationSize = 1
)
@GeneratedValue(strategy = GenerationType.TABLE, generator = "member_table_gen")
@Id
private Long id;
```

---

## 요약

- **IDENTITY**: 즉시 DB에서 키 생성 → INSERT 즉시 실행 → 배치 인서트 불가
- **SEQUENCE**: 시퀀스로 미리 ID 조회 후 INSERT → 배치 인서트 가능
- **TABLE**: 키 생성 테이블 사용 → 모든 DB 가능 → 성능은 떨어짐
- **AUTO**: DB 방언에 따라 전략 자동 선택 → 이식성 높음

# 쓰기 지연(Write-Behind)이란?

- **정의**: 엔티티 변경·저장 요청을 바로 DB에 반영하지 않고, 영속성 컨텍스트(1차 캐시)에 보관했다가  
  트랜잭션 커밋 시점이나 `flush()` 호출 시에 한 번에 DB에 반영하는 전략

---

## 동작 흐름

1. `em.persist()` 등으로 엔티티를 생성하거나 수정
2. JPA는 즉시 INSERT/UPDATE 쿼리를 보내지 않고,  
   변경 사항을 영속성 컨텍스트에 보관
3. 트랜잭션 커밋 시점 또는 `em.flush()` 호출 시점에  
   보관된 모든 쿼리를 한꺼번에 실행

---

## 장점

- **쿼리 최적화**: 여러 개의 INSERT/UPDATE를 묶어서 실행
- **트랜잭션 일관성**: 커밋 시점에만 실제 DB에 반영
- **불필요한 DB 호출 감소**: 중간에 여러 번 DB I/O를 줄임

---

## 단점

- **메모리 사용 증가**: 변경 내역을 메모리에 보관
- **즉시 반영 불가**: 중간에 조회 시 최신 상태가 DB에 없을 수 있음

---

## 요약

쓰기 지연은 “변경을 잠시 지연시켜 모았다가 한 번에 반영”하는 전략으로,  
쿼리 최적화와 일관성을 높이는 대신 메모리 사용량과 즉시성 측면을 고려해야 합니다.```
