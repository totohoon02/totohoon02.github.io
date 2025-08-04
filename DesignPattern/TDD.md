# TDD

- 테스트 주도 개발
- 로직 작성 이전에 테스트 코드를 먼저 작성한다.

## TDD 개발주기

### Red - Green - Blue

- Red
  - 실패하는 테스트 코드를 작성
- Green
  - 테스트를 성공시키기 위한 실제 코드를 작성
- Blue
  - 중복제거, 일반화, 리팩토링

> 기능 단위의 테스트 구현

- 설계가 변경되어도 수정에 필요한 리소스 감소

### TDD 예시

- 회원 가입 예시
- ID, PW, ROLE

#### 테스트 코드 작성

- Red

```java
public class UserJoinTest {

	private UserService userService;

	@BeforeEach
	void setUp() {
		userService = new UserService();
	}

	@Test
	public void 회원가입(){
		// given
		UserJoinDto dto = new UserJoinDto("kang", "password");
		dto.setRole("USER");

		// when
		User user = userService.join(dto);

		// then
		User findUser = userService.getUser(dto.getUsername());

		assertThat(findUser.getUsername()).isEqualTo(dto.getUsername());
	}
}

```

#### 필요한 코드 작성

- Green

```java
// User
@Getter
public class User {

	private final Long id;
	private final String username;
	private final String password;
	private String role;


	public User(Long id, UserJoinDto dto) {
		this.id = id;
		this.username = dto.getUsername();
		this.password = dto.getPassword();
		this.role = dto.getRole();
	}
}

// UserJoinDto
@Getter
public class UserJoinDto {

	private final String username;
	private final String password;
	@Setter
	private String role;


	public UserJoinDto(String username, String password) {
		this.username = username;
		this.password = password;
	}

}

// UserService
@Service
public class UserService {

	private final Map<String, User> users = new HashMap<>();
	public Long sequence = 0L;

	public User join(UserJoinDto dto) {
		User user = new User(++sequence, dto);
		users.put(dto.getUsername(), user);
		return user;
	}

	public User getUser(String username) {
		return users.get(username);
	}
}
```

- 테스트 시 통과

#### 구현체 작성, 리팩토링

- Blue
- 최소한의 코드(인메모리)를 실제 코드로 변환(DB)

```java
// UserService -> MemoryUserService로 변경

public interface UserService {
	public User join(UserJoinDto dto);
	
	public User getUser(String username);
}
// MemoryUserService <-- UserService 
// DbUserService     <--

// DbUserService
@Service
@Slf4j
@Transactional
@RequiredArgsConstructor
public class DbUserService implements UserService {
	
	private final UserRepository userRepository;
	
	@Override
	public User join(UserJoinDto dto) {
		log.info("join with UserService: DbUserService");
		User user = new User(dto);
		userRepository.save(user);
		return user;
	}
	
	@Override
	public User getUser(String username) {
		return userRepository.findByUsername(username).orElseThrow();
	}
}

// UserJoinTest
@SpringBootTest
@Transactional
public class UserJoinTest {
	
	@Autowired
	private UserService userService;
	
	@Test
	public void 회원가입(){
		// given
		UserJoinDto dto = new UserJoinDto("kang", "password");
		dto.setRole("USER");
		
		// when
		User user = userService.join(dto);
		
		// then
		User findUser = userService.getUser(dto.getUsername());
		
		assertThat(findUser.getUsername()).isEqualTo(dto.getUsername());
	}
}
```

- 테스트에 애너테이션 추가
- Memeory와 Db를 사용하는 각각의 구현체에서 동일한 테스트 코드로 검증
	- DbUserService 테스트에 필요한 리소스 감소
