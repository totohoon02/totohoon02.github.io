
# MCP와 AI Agent 차이

<img src="https://raw.githubusercontent.com/totohoon02/totohoon02.github.io/main/_images/agent_diagram.png" width="600" height="450">


## 📌 MCP(Model Context Protocol)
- **정의**  
  LLM이 **외부 기능(도구, 데이터, API, 실행 환경)** 과 안전하고 표준화된 방식으로 상호작용하도록 하는 **프로토콜**.
- **목적**  
  모델이 직접 DB 조회, API 호출, 코드 실행, 파일 읽기/쓰기 등을 할 수 있도록 하는 **연결 표준** 제공.
- **특징**
  - LLM 실행 환경(Host)와 외부 기능(Server)을 표준 인터페이스로 연결
  - 표준 메시지 포맷(JSON-RPC 기반) 사용
  - Host, Client, Server 구조
  - 예: VSCode에서 LLM이 플러그인을 MCP로 호출
- **비유**  
  AI가 사용할 수 있는 플러그인 API 표준 → MCP는 전화선 역할

---

## 📌 AI Agent
- **정의**  
  LLM이 목표를 달성하기 위해 **추론 + 계획 + 도구 호출**을 조합해서 실행하는 **시스템 또는 개체**.
- **목적**  
  단순 응답이 아니라, 상황에 맞춰 여러 단계의 작업을 스스로 수행.
- **특징**
  - 프롬프트 기반 추론
  - 필요한 경우 MCP, API, DB 등 외부 도구 호출
  - 상태(메모리) 관리 가능
  - 예: LangChain Agent, AutoGPT, CrewAI
- **비유**  
  문제를 해결하는 AI 비서 → 필요 시 MCP 같은 전화선을 통해 외부 기능 사용

---

## 📌 관계
- **MCP** = AI가 외부 도구를 쓸 수 있게 해주는 **연결 표준**
- **AI Agent** = 목표를 수행하는 **실행 주체**, MCP를 포함해 다양한 방법으로 도구 사용 가능

> MCP는 인프라(연결 규칙), AI Agent는 사용자(실행 주체)  
> Agent는 MCP를 쓸 수도 있고, 안 쓸 수도 있음.

---

## 📌 간단 비교표

| 구분 | MCP | AI Agent |
|------|-----|----------|
| 역할 | 연결 표준 | 실행 주체 |
| 초점 | LLM ↔ 도구 연결 | 목표 달성 |
| 구성 요소 | Host, Client, Server | 추론, 계획, 도구 호출, 메모리 |
| 예시 | VSCode + MCP 플러그인 | LangChain Agent, AutoGPT |
| 의존성 | 독립적으로 존재 가능 | MCP를 사용할 수도 있음 |

