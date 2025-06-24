# 스프링 예외처리

## 스프링 예외처리 개요

스프링 프레임워크는 애플리케이션에서 발생하는 예외를 효과적으로 처리하기 위한 다양한 메커니즘을 제공합니다. 예외 처리는 애플리케이션의 안정성과 사용자 경험을 향상시키는 중요한 부분입니다.

## 스프링 예외처리 방식

### 1. try-catch 방식

가장 기본적인 자바 예외 처리 방식으로, 메소드 내에서 직접 예외를 잡아 처리합니다.

```java
public String processData(String input) {
    try {
        // 데이터 처리 로직
        return processedData;
    } catch (Exception e) {
        log.error("데이터 처리 중 오류 발생: {}", e.getMessage());
        return "오류가 발생했습니다.";
    }
}
```

### 2. throws 키워드 사용

메소드 시그니처에 throws 키워드를 사용하여 예외를 호출자에게 전파합니다.

```java
public String processData(String input) throws DataProcessingException {
    // 데이터 처리 로직
    if (somethingWrong) {
        throw new DataProcessingException("데이터 처리 중 오류 발생");
    }
    return processedData;
}
```

- `RuntimeException`의 경우 throws가 없어도 예외를 던진다.

### 3. @ExceptionHandler

컨트롤러 클래스 내에서 특정 예외를 처리하는 메소드를 정의합니다.

```java
@Controller
public class UserController {

    @GetMapping("/users/{id}")
    public String getUser(@PathVariable Long id) {
        // 사용자 조회 로직
        return "user-details";
    }

    @ExceptionHandler(UserNotFoundException.class)
    public ResponseEntity<ErrorResponse> handleUserNotFoundException(UserNotFoundException ex) {
        ErrorResponse error = new ErrorResponse(HttpStatus.NOT_FOUND.value(), ex.getMessage());
        return new ResponseEntity<>(error, HttpStatus.NOT_FOUND);
    }
}
```

- `UserNotFoundException` 발생 시 `handleUserNotFoundException` 메서드에서 예외 처리
- 예외마다 각각 예외처리 메서드를 정의해도 됨

### 4. @ControllerAdvice / @RestControllerAdvice

전역적으로 예외를 처리하는 클래스를 정의합니다. 여러 컨트롤러에서 발생하는 동일한 예외를 한 곳에서 처리할 수 있습니다.

```java
@RestControllerAdvice
public class GlobalExceptionHandler {

    @ExceptionHandler(UserNotFoundException.class)
    public ResponseEntity<ErrorResponse> handleUserNotFoundException(UserNotFoundException ex) {
        ErrorResponse error = new ErrorResponse(HttpStatus.NOT_FOUND.value(), ex.getMessage());
        return new ResponseEntity<>(error, HttpStatus.NOT_FOUND);
    }

    @ExceptionHandler(ValidationException.class)
    public ResponseEntity<ErrorResponse> handleValidationException(ValidationException ex) {
        ErrorResponse error = new ErrorResponse(HttpStatus.BAD_REQUEST.value(), ex.getMessage());
        return new ResponseEntity<>(error, HttpStatus.BAD_REQUEST);
    }

    @ExceptionHandler(Exception.class)
    public ResponseEntity<ErrorResponse> handleGenericException(Exception ex) {
        ErrorResponse error = new ErrorResponse(HttpStatus.INTERNAL_SERVER_ERROR.value(), "서버 내부 오류가 발생했습니다.");
        return new ResponseEntity<>(error, HttpStatus.INTERNAL_SERVER_ERROR);
    }
}
```

- 모든 `@Controller` 또는 `@RestController`에서 발생하는 예외를 잡아 처리

### 5. ResponseStatusException

Spring 5부터 도입된 방식으로, 예외 클래스를 별도로 생성하지 않고 HTTP 상태 코드와 메시지를 직접 지정할 수 있습니다.

```java
@GetMapping("/users/{id}")
public User getUser(@PathVariable Long id) {
    User user = userService.findById(id);
    if (user == null) {
        throw new ResponseStatusException(HttpStatus.NOT_FOUND, "사용자를 찾을 수 없습니다.");
    }
    return user;
}
```

## 커스텀 예외 클래스 생성

비즈니스 로직에 맞는 예외 클래스를 직접 정의할 수 있습니다.

```java
@ResponseStatus(HttpStatus.NOT_FOUND)
public class UserNotFoundException extends RuntimeException {
    public UserNotFoundException(String message) {
        super(message);
    }
}

@ResponseStatus(HttpStatus.BAD_REQUEST)
public class ValidationException extends RuntimeException {
    public ValidationException(String message) {
        super(message);
    }
}
```

## 종합 예제

### 1. 커스텀 예외 클래스 정의

```java
public class ResourceNotFoundException extends RuntimeException {
    public ResourceNotFoundException(String resourceName, String fieldName, Object fieldValue) {
        super(String.format("%s not found with %s : '%s'", resourceName, fieldName, fieldValue));
    }
}

public class BadRequestException extends RuntimeException {
    public BadRequestException(String message) {
        super(message);
    }
}
```

### 2. 에러 응답 클래스 정의

```java
@Getter
@Setter
public class ErrorResponse {
    private Date timestamp;
    private int status;
    private String message;
    private String details;

    public ErrorResponse(int status, String message, String details) {
        this.timestamp = new Date();
        this.status = status;
        this.message = message;
        this.details = details;
    }
}
```

### 3. 전역 예외 처리기 구현

```java
@RestControllerAdvice
public class GlobalExceptionHandler {

    @ExceptionHandler(ResourceNotFoundException.class)
    public ResponseEntity<ErrorResponse> handleResourceNotFoundException(ResourceNotFoundException ex, WebRequest request) {
        ErrorResponse errorResponse = new ErrorResponse(
            HttpStatus.NOT_FOUND.value(),
            ex.getMessage(),
            request.getDescription(false));
        return new ResponseEntity<>(errorResponse, HttpStatus.NOT_FOUND);
    }

    @ExceptionHandler(BadRequestException.class)
    public ResponseEntity<ErrorResponse> handleBadRequestException(BadRequestException ex, WebRequest request) {
        ErrorResponse errorResponse = new ErrorResponse(
            HttpStatus.BAD_REQUEST.value(),
            ex.getMessage(),
            request.getDescription(false));
        return new ResponseEntity<>(errorResponse, HttpStatus.BAD_REQUEST);
    }

    @ExceptionHandler(Exception.class)
    public ResponseEntity<ErrorResponse> handleGlobalException(Exception ex, WebRequest request) {
        ErrorResponse errorResponse = new ErrorResponse(
            HttpStatus.INTERNAL_SERVER_ERROR.value(),
            ex.getMessage(),
            request.getDescription(false));
        return new ResponseEntity<>(errorResponse, HttpStatus.INTERNAL_SERVER_ERROR);
    }

    // MethodArgumentNotValidException 처리 (Bean Validation 예외)
    @ExceptionHandler(MethodArgumentNotValidException.class)
    public ResponseEntity<ErrorResponse> handleValidationExceptions(MethodArgumentNotValidException ex, WebRequest request) {
        Map<String, String> errors = new HashMap<>();
        ex.getBindingResult().getAllErrors().forEach((error) -> {
            String fieldName = ((FieldError) error).getField();
            String errorMessage = error.getDefaultMessage();
            errors.put(fieldName, errorMessage);
        });

        ErrorResponse errorResponse = new ErrorResponse(
            HttpStatus.BAD_REQUEST.value(),
            "유효성 검사 실패",
            errors.toString());
        return new ResponseEntity<>(errorResponse, HttpStatus.BAD_REQUEST);
    }
}
```

### 4. 서비스 레이어에서 예외 발생시키기

```java
@Service
public class UserService {

    @Autowired
    private UserRepository userRepository;

    public User getUserById(Long id) {
        return userRepository.findById(id)
            .orElseThrow(() -> new ResourceNotFoundException("User", "id", id));
    }

    public User createUser(User user) {
        if (userRepository.existsByEmail(user.getEmail())) {
            throw new BadRequestException("이미 등록된 이메일 주소입니다.");
        }
        return userRepository.save(user);
    }
}
```

### 5. 컨트롤러에서 서비스 사용하기

```java
@RestController
@RequestMapping("/api/users")
public class UserController {

    @Autowired
    private UserService userService;

    @GetMapping("/{id}")
    public ResponseEntity<User> getUserById(@PathVariable(value = "id") Long userId) {
        User user = userService.getUserById(userId);
        return ResponseEntity.ok().body(user);
    }

    @PostMapping
    public ResponseEntity<User> createUser(@Valid @RequestBody User user) {
        User savedUser = userService.createUser(user);
        return ResponseEntity.created(URI.create("/api/users/" + savedUser.getId())).body(savedUser);
    }
}
```

## 예외 처리 모범 사례

1. **계층별 예외 처리**: 각 계층(컨트롤러, 서비스, 리포지토리)에 맞는 예외 처리 전략을 수립합니다.

2. **예외 변환**: 하위 계층의 예외를 상위 계층에서 이해할 수 있는 예외로 변환합니다.

3. **예외 로깅**: 예외 발생 시 적절한 로깅을 통해 문제 해결에 필요한 정보를 기록합니다.

4. **사용자 친화적 메시지**: 최종 사용자에게는 기술적 상세 정보 대신 이해하기 쉬운 메시지를 제공합니다.

5. **예외 계층 구조**: 비즈니스 도메인에 맞는 예외 계층 구조를 설계합니다.
