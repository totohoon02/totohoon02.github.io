## MCP (Model Context Protocol)

- MCP(Model Context Protocol)는 AI 모델이 외부 도구와 상호작용하며 보다 정교한 작업을 수행할 수 있도록 지원하는 프로토콜입니다.
- 이는 특정 프레임워크에 종속되지 않고 독립적으로 동작할 수 있도록 설계되었습니다.

### 주요 특징

- **Tool 역할을 담당**
  - LLM이 다양한 외부 도구를 호출하고 활용할 수 있도록 함.
- **Cursor AI 및 Claude Desktop과 호환**
  - Cursor AI, Claude Desktop 등 다양한 AI 애플리케이션에서 활용 가능.
- **프로토콜 및 프레임워크 독립적**
  - 특정 플랫폼(LangChain, LangGraph 등)에 종속되지 않으며, 유연하게 확장 가능.

---

## How to use?

### Claude + smithery

- Claude 데스크탑 설치
- 설정파일에 mcp 서버 추가
  `C:\Users\User\AppData\Roaming\Claude\claude_desktop_config.json`
- Node 설치
  - 윈도우 - 파워쉘 - 관리자모드 실행
    `Set-ExecutionPolicy Unrestricted -Scope CurrentUser`
  - npx가 실행되면 준비완료

```bash
{
	"mcpServers": {
		"todoist-mcp-server": {
			"command": "cmd",
			"args": [
				"/c",
				"npx",
				"-y",
				"@smithery/cli@latest",
				"run",
				"@abhiz123/todoist-mcp-server",
				"--config",
				"{\"todoistApiToken\":\"토큰토큰\"}"
			]
		}
	}
}

```

### Resource

- 필요한 정보와 컨텍스트 제공

```python
@mcp.resource("greeting://{name}")
def get_greeting(name: str) -> str:
    """Get a personalized greeting"""
    return f"Hello, {name}!"
```

### Tools

- 작업 수행

```python
@mcp.tool()
def add(a: int, b: int) -> int:
    """Add two numbers"""
    return a + b
```

### Prompt

- 일관된 응답 생성

```python
@mcp.prompt()
def review_code(code: str) -> str:
	return f"Please revicew this code \n\n {code}"
```

[REFERENCE]

- [MCP(Model Context Protocol)이 뭐길래? 실습편](https://dytis.tistory.com/113)
- [MCP란 무엇인가? — AI의 새로운 문법](https://datasciencebeehive.tistory.com/264)
