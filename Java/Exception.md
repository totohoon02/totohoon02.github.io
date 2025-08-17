# 자바의 예외(Exception) 처리

## Checked Exception과 Unchecked Exception

자바에서 예외는 크게 두 가지 유형으로 나뉩니다: **Checked Exception**과 **Unchecked Exception**입니다.

### Checked Exception

- **컴파일 시점**에 확인되는 예외
- 반드시 예외 처리를 해야 함 (try-catch 또는 throws)
- Exception 클래스를 상속받지만 RuntimeException을 상속받지 않는 예외들

#### 주요 Checked Exception 예시

- `IOException`: 입출력 작업 중 발생하는 예외
- `SQLException`: 데이터베이스 작업 중 발생하는 예외
- `ClassNotFoundException`: 클래스를 찾을 수 없을 때 발생하는 예외
- `FileNotFoundException`: 파일을 찾을 수 없을 때 발생하는 예외

#### 예시 코드

```java
public void readFile(String path) throws IOException {
    // throws를 사용하여 호출자에게 예외 처리 위임
    FileReader file = new FileReader(path);
    // 파일 처리 로직
}

// 또는

public void readFile(String path) {
    try {
        FileReader file = new FileReader(path);
        // 파일 처리 로직
    } catch (IOException e) {
        // 예외 처리 로직
        e.printStackTrace();
    }
}
```

### Unchecked Exception

- **런타임 시점**에 발생하는 예외
- 컴파일러가 예외 처리를 강제하지 않음
- RuntimeException 클래스를 상속받는 예외들

#### 주요 Unchecked Exception 예시

- `NullPointerException`: null 객체의 메서드를 호출할 때 발생
- `ArrayIndexOutOfBoundsException`: 배열의 범위를 벗어난 인덱스에 접근할 때 발생
- `IllegalArgumentException`: 메서드에 부적절한 인자가 전달될 때 발생
- `ArithmeticException`: 수학적 오류(예: 0으로 나누기)가 발생할 때

#### 예시 코드

```java
public int divide(int a, int b) {
    // Unchecked Exception은 명시적인 예외 처리가 필요 없음
    return a / b;  // b가 0이면 ArithmeticException 발생
}

// 필요에 따라 예외 처리 가능
public int safeDivide(int a, int b) {
    try {
        return a / b;
    } catch (ArithmeticException e) {
        System.out.println("0으로 나눌 수 없습니다.");
        return 0;  // 기본값 반환
    }
}
```

## 언제 어떤 예외를 사용해야 할까?

### Checked Exception 사용 시기

- **외부 환경**과의 상호작용에서 발생할 수 있는 예외
- 호출자가 **복구할 가능성**이 있는 예외
- 예측 가능하고 대응 방법이 명확한 예외 상황

**예시 상황:**
- 파일 입출력 작업
- 네트워크 통신
- 데이터베이스 연결 및 쿼리 실행

### Unchecked Exception 사용 시기

- **프로그래밍 오류**로 인한 예외
- 호출자가 **복구할 가능성이 낮은** 예외
- 예방이 가능하고 사전에 검증해야 하는 예외 상황

**예시 상황:**
- 잘못된 인덱스 접근
- null 참조
- 잘못된 형식의 인자 전달

## Error와 Exception의 차이

### Error

- JVM 레벨에서 발생하는 심각한 문제
- 애플리케이션 코드에서 **복구가 불가능**한 상황
- 일반적으로 예외 처리를 하지 않음

**주요 Error 예시:**
- `OutOfMemoryError`: 메모리 부족 오류
- `StackOverflowError`: 스택 오버플로우 오류
- `NoClassDefFoundError`: 클래스 정의를 찾을 수 없는 오류

### Exception

- 프로그램 실행 중 발생할 수 있는 예외적 상황
- 대부분 **복구 가능**한 상황
- 적절한 예외 처리를 통해 프로그램 실행을 계속할 수 있음

## 예외 처리 모범 사례

1. **구체적인 예외 처리**: 가능한 구체적인 예외를 잡아 처리하세요.
2. **예외 전환**: 저수준 예외를 잡아 더 의미 있는 고수준 예외로 변환하세요.
3. **리소스 정리**: try-with-resources 구문을 사용하여 자원을 자동으로 정리하세요.
4. **예외 로깅**: 예외 발생 시 충분한 정보를 로깅하세요.
5. **예외 문서화**: 메서드가 발생시킬 수 있는 예외를 JavaDoc으로 문서화하세요.

```java
/**
 * 파일에서 데이터를 읽어옵니다.
 * @param path 파일 경로
 * @return 파일 내용
 * @throws FileNotFoundException 파일을 찾을 수 없는 경우
 * @throws IOException 파일 읽기 중 오류가 발생한 경우
 */
public String readData(String path) throws FileNotFoundException, IOException {
    // 구현 코드
}
```

## 결론

자바의 예외 처리 메커니즘은 프로그램의 안정성ㅁ과 견고성을 높이는 중요한 요소입니다. Checked Exception과 Unchecked Exception을 적절히 활용하여 예외 상황을 효과적으로 관리하는 것이 중요합니다. 상황에 맞는 예외 처리 전략을 선택하고, 일관된 예외 처리 패턴을 유지하는 것이 좋은 코드 작성의 핵심입니다.
