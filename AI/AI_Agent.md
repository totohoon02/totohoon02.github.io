## AI Agent

- AI Agent는 LLM을 활용하여 특정 작업을 수행하는 지능형 시스템입니다.
- 이 시스템은 다양한 도구(Tools)와 함께 동작하며, 주어진 문제를 해결하기 위해 사고(Reasoning)와 행동(Acting)을 반복적으로 수행합니다.

### 구성 요소

- **LLM (Large Language Model)**
  - 주요 추론 및 응답 생성을 담당하는 대형 언어 모델.
- **Tools**
  - LLM이 단독으로 해결하기 어려운 작업을 수행하기 위한 보조 도구들.
  - 예시:
    - **웹 검색**: 실시간으로 인터넷 검색을 수행하여 최신 정보를 제공.
    - **문서 검색**: RAG(Retrieval-Augmented Generation) 기법을 활용하여 사전 정의된 문서에서 정보를 추출.
    - **코드 실행**: Python 등의 실행 환경에서 코드 실행 후 결과를 반환.
    - **Custom Tool**: 특정 비즈니스 로직에 맞춘 사용자 정의 도구.
- **ReAct (Reasoning + Acting)**
  - LLM이 사고(Reasoning)와 행동(Acting)을 반복하며 최적의 답을 도출하는 기법.
  - 주요 단계:
    1. **Thought (사고)**: 현재 상태에서 어떤 행동이 필요한지 결정.
    2. **Action (행동)**: 결정한 행동을 실행 (예: 검색, 계산, 코드 실행 등).
    3. **Observation (관찰)**: 행동의 결과를 분석.
    4. **Logs (기록)**: 전체 수행 과정의 로그를 저장.
    5. **Loop (반복)**: 목표를 달성할 때까지 위 과정을 반복.

### 예시 코드
```python
from langchain.agents import initialize_agent, Tool
from langchain.agents.agent_types import AgentType
from langchain_openai import ChatOpenAI
from langchain_community.tools import DuckDuckGoSearchRun

# 1. LLM 구성
llm = ChatOpenAI(model="gpt-4", temperature=0)

# 2. Tool 정의 (DuckDuckGo 웹 검색)
search_tool = DuckDuckGoSearchRun()

tools = [
    Tool(
        name="Web Search",
        func=search_tool.run,
        description="실시간 정보를 찾을 수 있는 검색 도구"
    )
]

# 3. Agent 초기화 (ReAct 기반)
agent = initialize_agent(
    tools,
    llm,
    agent=AgentType.ZERO_SHOT_REACT_DESCRIPTION,
    verbose=True  # 콘솔에 로그 출력
)

# 4. 프롬프트로 에이전트 실행
prompt = "서울의 오늘 날씨를 검색하고, 평균 기온이 20도 이하이면 '겉옷 챙기세요!'라고 말해줘."

response = agent.run(prompt)

print("\n📌 결과:", response)
```
```
> Entering new AgentExecutor chain...
Thought: 서울의 날씨를 알아야 한다.
Action: Web Search
Action Input: 서울의 오늘 날씨
Observation: 오늘 서울은 흐림, 최고기온 19도, 최저기온 14도입니다.
Thought: 평균 기온은 약 16.5도로 20도 이하이다.
Final Answer: 겉옷 챙기세요!
```

