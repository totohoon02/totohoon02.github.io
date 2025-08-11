# MCP (Model Context Protocol)

- 모델이 외부 기능과 구조적으로 상호작용 하기 위한 표준 프로토콜
- LLM 모델이 직접 코드 실행, DB 조회, API 호출할 수 있게 허용

## 역할

- Host
  - LLM이 실행되는 환경
  - Cursor, VSCode
  - Client를 포함하거나 연결
  - 입출력, MCP 호출을 오케스트레이션
- Client
  - Host 내부에서 Server와 통신하는 중간자
  - LLM의 도구 호출을 표준 MCP 요청 포맷으로 변환
  - 응답을 받아 LLM이 이해할 수 있도록 변환
- Server
  - MCP 표준을 구현한 기능 제공자

```
[User]
   ↓
[LLM in Host] ←──> [MCP Client] ←──> [MCP Server #1] (예: DB 조회)
                        │
                        └──> [MCP Server #2] (예: API 호출)
```

## 동작 원리

- JSON-RPC/HTTP

```python
{
  "jsonrpc": "2.0",
  "id": "123",
  "method": "callTool",
  "params": {
    "name": "get_weather",
    "arguments": { "city": "Seoul" }
  }
}
```

1. 연결
   - Client가 Server와의 세션 생성
   - Server에서 Tool 목록과 Schema를 Client에 전달
   - 해당 정보를 LLM에 등록
2. 요청
   - LLM이 대화 중 `get_weather`라는 Tool 호출 필요 판단
   - Client가 `JSON-RPC`형식으로 호출 파라미터 생성
   - Server로 JSON 전달
3. 처리
   - Server는 Tool 실행
   - 결과를 JSON Schema 형식으로 직렬화
4. 응답
   - Client로 응답 전송
   - Client가 LLM이 이해할 수 있는 형식으로 전달
   - LLM은 응답을 반영해 사용자에 보여줌
