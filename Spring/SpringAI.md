# Spring AI

**[REFERENCE]**

- [SpringAI Docuemnt](https://docs.spring.io/spring-ai/reference/index.html)
- [개발자 유미](https://www.devyummi.com/page?id=685ddbd8672dadce3b975a03)
- [전체 코드](https://github.com/totohoon02/spring-base/tree/main/aifollow)

### ChromaDB Docker

<details>
<summary>docker-compose.yml</summary>
<div markdown="1">

```yml
services:
  chromadb:
    command:
      ["run", "--host", "0.0.0.0", "--port", "8000", "--path", "/chroma/chroma"]
    container_name: chromadb
    environment:
      - IS_PERSISTENT=TRUE
      - PERSIST_DIRECTORY=/chroma/chroma
      - ANONYMIZED_TELEMETRY=FALSE
    image: ghcr.io/chroma-core/chroma:latest
    # networks:
    #   - mynetwork
    ports:
      - 8000:8000
    restart: always
    volumes:
      - ./data:/chroma/chroma
# networks:
#   mynetwork:
#     external: true
```

</div>
</details>

### Dependency

```gradle
// model-*이 core 패키지 포함
implementation 'org.springframework.ai:spring-ai-starter-model-openai'

// vector DB & advisor
implementation 'org.springframework.ai:spring-ai-advisors-vector-store'
implementation 'org.springframework.ai:spring-ai-starter-vector-store-chroma'

// lombok
compileOnly 'org.projectlombok:lombok'
annotationProcessor 'org.projectlombok:lombok'
testCompileOnly 'org.projectlombok:lombok'
testAnnotationProcessor 'org.projectlombok:lombok'
```

## Config

- application.yml

```yml
// chroma & openai
spring:
  ai:
    vectorstore:
      chroma:
        client:
          host: http://localhost
          port: 8000
        initialize-schema: true # auto gen collection
        collection-name: springAi

spring:
  ai:
    openai:
      api-key: <api-key-here>
      chat:
        options:
          model: 'gpt-4o-mini'
          temperature: 0.8
      embedding:
        options:
          model: 'text-embedding-3-small'
```

- AiConfig.java

```java
@Configuration
public class AiConfig {

	@Bean
	public ChatMemoryRepository chatMemoryRepository() {
		return new InMemoryChatMemoryRepository(); // Default Value
	}

	@Bean
	public ChatMemory chatMemory(ChatMemoryRepository chatMemoryRepository) {
		return MessageWindowChatMemory.builder()
				.maxMessages(10)
				.chatMemoryRepository(chatMemoryRepository)
				.build();
	}

	// Client로 래핑해서 쓸 때 Bean 생성 필요
	@Bean
	ChatClient chatClient(ChatModel chatModel) {
		return ChatClient.create(chatModel);
	}
}
```

### Chat

```java
private final ChatClient chatClient;
private final ChatMemory chatMemory;
private final ChatMemoryRepository chatMemoryRepository;

// just chat
@Override
public String chat(String message) {
	Prompt prompt = new Prompt(new UserMessage(message));
	return chatClient.prompt(prompt).call().content();
}

// Multiturn chat
@Override
public Flux<String> multiTurnChat(String message) {
	chatMemory.add(userId, new UserMessage(message));

	Prompt prompt = new Prompt(chatMemory.get(userId));

	// 응답 메시지를 임시 보관
	StringBuilder responseBuffer = new StringBuilder();

	return chatClient
			.prompt(prompt)
			.stream()
			.content() // Flux<String> 토큰/델타
			.filter(token -> token != null && !token.isBlank())
			.doOnNext(responseBuffer::append) // 전체 응답 누적
			.doOnComplete(() -> {
				// 스트림 정상 종료 시 대화 저장
				if (!responseBuffer.isEmpty()) {
					chatMemory.add(userId, new AssistantMessage(responseBuffer.toString()));
					chatMemoryRepository.saveAll(userId, chatMemory.get(userId));
				}
			})
			.onErrorResume(ex -> {
				// 오류 시 대응 (원하면 커스텀 메시지 전송)
				log.error("LLM streaming error", ex);
				return Mono.empty();
			});
}

// Structured Output
@Override
public CountryInfoDTO structuredOutput(String koName) {
	SystemMessage systemMessage = new SystemMessage("한국어로 된 국가명을 입력 받아 해당 국가의 공식 영어 명칭과 수도를 반환");
	UserMessage userMessage = new UserMessage(koName);

	Prompt prompt = new Prompt(List.of(systemMessage, userMessage));
	return chatClient.prompt(prompt)
			.call()
			.entity(CountryInfoDTO.class);
}

// Tool Calling
@Override
public CountryInfoDTO toolCalling(String koName) {
	SystemMessage systemMessage = new SystemMessage("한국어로 된 국가명을 입력 받아 해당 국가의 공식 영어 명칭과 수도를 반환");
	UserMessage userMessage = new UserMessage(koName);

	Prompt prompt = new Prompt(List.of(systemMessage, userMessage));
	return chatClient.prompt(prompt)
			.tools(new DBTool())
			.call()  // 동기 호출
			.entity(CountryInfoDTO.class); // 응답을 DTO로 매핑
}
```

#### Chat Test

```java
@Slf4j
@SpringBootTest
public class ChatTest {

	@Autowired
	private ChatService chatService;

	@Test
	void 동작_테스트(){
		String message = "안녕하세요?";
		String call = chatService.chat(message);
		log.info(call);
		assertThat(call).isNotEmpty();
	}

	@Test
	void 멀티턴_아닌_멀티턴_테스트(){
		// 기억하지 않음
		chatService.chat("미니언들은 대화를 할 때 각 단어의 첫 글자를 b로 바꿔 예를들면 my name is hoon는 by bame bs boon이라고 말해");
		String response = chatService.chat("그럼 how are you? 는 뭐라고 말할까? 응답만 출력해줘");
		log.info("response: {}", response);
		assertThat(response).isNotEqualTo("bow bar bou");
	}

	@Test
	void 멀티턴인_멀티턴_테스트(){
		chatService.multiTurnChat("미니언들은 대화를 할 때 각 단어의 첫 글자를 b로 바꿔 예를들면 my name is hoon는 by bame bs boon이라고 말해")
				.blockLast();

		String response = chatService.multiTurnChat("그럼 how are you? 는 뭐라고 말할까? 응답만 출력해줘")
				.collect(Collectors.joining())
				.block();

		log.info("response: {}", response);
		assertThat(response).isNotEmpty();
	}

	@Test
	void Struectured_Output_테스트(){
		CountryInfoDTO countryInfoDTO = chatService.structuredOutput("미국");
		log.info(countryInfoDTO.toString());
		assertThat(countryInfoDTO.enName()).isEqualTo("United States of America");
	}

	@Test
	void 툴_호출_테스트() {
		CountryInfoDTO call1 = chatService.toolCalling("프랑스");
		log.info("call result: {}", call1);
		assertThat(call1.enName()).isEqualTo("France");

		CountryInfoDTO call2 = chatService.toolCalling("부카니스탄");
		log.info("call result: {}", call2);
		assertThat(call2.enName()).isEqualTo("Unknown");
	}
}
```

### Embedding

```java
private final ChatClient chatClient;
private final EmbeddingModel embeddingModel;

private final VectorStore vectorStore;


// 임베딩 생성(1536 차원)
@Override
public float[] embed(String content) {
	float[] embed = embeddingModel.embed(content);
	return embed;
}

// 벡터DB 저장
@Override
public void add(String content) {
	Document document = new Document(content);
	vectorStore.add(List.of(document));
}

// 벡터DB 검색
@Override
public List<Document> search() {
	return vectorStore.similaritySearch("가나다라마바사아자차카타파하abcedfghijklmnopqrstuvwxyz");
}

// RAG 적용
@Override
public String searchWithAdvisor() {
	// 검색 옵션 설정
	SearchRequest request = SearchRequest.builder()
			.similarityThreshold(0.5d)
			.topK(5)
			.build();

	// 벡터디비를 참고할 수 있게 연결
	Advisor advisor = QuestionAnswerAdvisor.builder(vectorStore)
			.searchRequest(request)
			.build();

	return chatClient.prompt(new Prompt("미니언이 하는 말은?"))
			.advisors(advisor)
			.call()
			.content();
}
```

#### Embedding Test

```java
@Slf4j
@SpringBootTest
public class EmbeddingTest {

	@Autowired
	private EmbeddingService embeddingService;

	@Test
	void 임베딩_테스트() {
		float[] embed = embeddingService.embed("안녕하세요");
		log.info("vector= {}", embed);
		assertThat(embed).hasSize(1536);
	}

	@Test
	void 데이터_추가_테스트() {
		// 한번만 실행
		embeddingService.add("미니언이 하는 말은 '빠까뽀'이다.");
	}

	@Test
	void 검색_테스트() {
		List<Document> documents = embeddingService.search();
		log.info("search: {}", documents);
		assertThat(documents).isNotEmpty();
	}

	@Test
	void 어드바이저_테스트() {
		String response = embeddingService.searchWithAdvisor();
		log.info("response: {}", response);
	}
}
```
