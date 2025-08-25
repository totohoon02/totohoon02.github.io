# Transactional

- 프록시 방식의 AOP
- 프록시 객체가 요청을 받아서 트랜잭션 처리
- 이후 대상 객체 호출
- 내부 호출 시에 트랜잭션이 적용되지 않음
  - 객체를 직접 호출

### 트랜잭션 처리 과정

1. 메서드 호출
   - `@Transactional`이 붙은 메서드를 호출
   - 프록시 객체 생성, `TransactionInterceptor`를 통해 트랜잭션 로직 실행
2. 트랜잭션 매니저가 트랜잭션을 시작
   - `PlatformTransactionManager`가 커넥션 획득
   - `AutoCommint=False`
   - 물리 트랜잭션 시작
3. 비지니스 로직 실행
   - 실제 코드를 실행
   - 예외 발생 시
     - `RuntimeException` / `Error` → 스프링이 `rollback-only`로 마킹
     - `Checked Exception` → 기본적으로 커밋 (옵션으로 롤백 가능)
4. 정상/예외 처리에 따른 커밋 또는 롤백
   - 메서드의 실행이 끝나면 프록시 객체는 트랜잭션 매니저에 실행 결과를 보고
   - rollback-only가 true → 롤백
   - 아니면 → 커밋
   - `commit()` 또는 `rollback()` 호출
5. 자원 반환

```java
class CallService{
	void external(){
		//
		this.internal(); // 내부 메서드를 직접 호출
	}

	@Transactional
	void internal(){
		//
	}
}
```

- external를 호출 시에 트랜잭션이 적용 안됨
- internal를 외부 클래스로 분리

```java
class CallService{

	private InternalService internalService;

	public void external(){
		//
		internalService.internal();
	}
}

class InternalService{
	@Transactional
	public void internal(){
		//
	}
}
```

- `public` 메서드에만 적용

```java
@PostConstruct
@Transactional
public void initV1(){
	// 초기화 코드 호출 -> AOP 적용
}
```

- 스프링 초기화 시에는 트랜잭션이 적용되지 않는다.
- 호출 순서 차이

### 트랜잭션 옵션

- `value`
  - 트랜잭션 매니저가 복수일 때(사용하는 경우는 거의 없음)
- `rollbackFor`
  - 특정 예외 발생 시 롤백
- `noRollBackFor`
  - 특정 예외 발생 시 롤백 안함
- `isolation`
  - 트랜잭션 격리 수준 지정
  - 보통 데이터베이스에 설정된 기준을 따르고 직접 설정하는 경우는 드물다.
- `timeout`
  - 초 단위
- `readOnly`
  - 트랜잭션은 기본적으로 읽기/쓰기 가능
  - 읽기 전용
  - 읽기 최적화가 발생할 수 있음
    - 프레임워크
    - JPA에서 커밋 시점에 플러시 x
  - JDBC 드라이버
    - 읽기, 쓰기 데이터베이스를 구분해서 요청
  - 데이터베이스
    - 읽기 전용 트랜잭션의 경우 내부에서 최적화
