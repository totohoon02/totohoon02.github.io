# Transcation Propagation

- 트랜잭션은 `커밋`되거나 `롤백`되어서 끝난다.
- `트랜잭션 전파`는 트랜잭션이 동작 중에 다른 트랜잭션을 실행할 경우 어떻게 처리할 것인가에 대한 문제

## 논리 트랜잭션과 물리 트랜잭션

- 물리 트랜잭션
  - DB 커넥션 레벨에서 실제로 시작되는 트랜잭션
- 논리 트랜잭션
  - 스프링에서 `@Transactional`로 관리되는 트랜잭션의 단위
  - 여러개의 논리 트랜잭션이 하나의 물리 트랜잭션을 공유할 수 있다.

## 트랜잭션 전파 옵션

| 전파 타입         | 설명                                                       |
| ----------------- | ---------------------------------------------------------- |
| `REQUIRED` (기본) | 현재 트랜잭션이 있으면 참여, 없으면 새로 생성              |
| `REQUIRES_NEW`    | 항상 새로운 물리 트랜잭션 시작 (기존 트랜잭션은 잠시 중단) |

## 외부 롤백 vs 내부 롤백

### 외부 롤백

- 부모에서 예외 발생

```java
@Transactional
public void outer() {
    inner(); // 내부 메서드 호출
    throw new RuntimeException("외부에서 예외");
}

@Transactional
public void inner() {
    insertData();
}
```

- `REQUIRE`
  - outer 예외 -> 전체 롤백
- `REUIREWS_NEW`
  - outer에서 예외 발생해도 inner는 새로운 물리 트랜잭션, 커밋
  - 부분 커밋 발생

### 내부 롤백

- 자식에서 예외 발생

```java
@Transactional
public void outer() {
    try {
        inner();
    } catch(Exception e) {
        log.error("내부 예외 처리");
    }
    updateData();
}

@Transactional
public void inner() {
    insertData();
    throw new RuntimeException("내부에서 예외");
}

```

- `REQUIRE`
  - inner에서 예외가 발생, `rollbackOnly=True`, 에러를 잡아도 전체 롤백
  - 스프링 내부적으로 rollbackOnly 기록
- `REUIREWS_NEW`
  - inner만 롤백, outer는 커밋
