# LangChain

## 00. LLM 기초

<details>
<summary>Contents</summary>
<div markdown="1">

## Algorithm vs ML

- 룰 베이스, 엔지니어가 작성 vs 방대한 데이터를 기반으로 알고리즘을 학습

> 소프트웨어 엔지니어의 역할 알고리즘 작성 -> 알고리즘을 학습하는 모델 개발

## ML vs LLM

- 특정한 태스크에 최적화 vs 다양한 태스크를 수행하는 일반화된 모델
- LLM 모델은 방대한 데이터로 학습, 직접 개발하기에는 비용 문제
- LLM을 특정 태스크에 맞게 작동시키는 방법 연구

## LLM 기초

**ANN 기반의 태스크들의 원리**

> 1. 입력을 바탕으로 고차원의 잠재 벡터(Latent Vector)를 생성
> 2. 잠재 벡터를 어떻게 표현하는지에 따라 텍스트, 이미지를 생성
> 3. 잠재 벡터에 약간의 노이즈를 추가하여 다양한 결과를 생성
> 4. 이전의 RNN, CNN 기반 구조가 Transformer 기반으로 변경됨

- 거대 언어 모델(LLM)

  - 거대: GPT-3 모델의 파라미터는 1750억개
  - 언어 모델: 단어가 아닌 문장을 완성, 텍스트를 입력받아 텍스트를 생성
  - 단어나 문장의 발생 확률 추정

- 프롬프트(Prompt)

  - LLM이 생성할 텍스트를 제어하는 방법
  - 출력 형식과 제약 조건을 명시적으로 전달
  - 프롬프트 엔지니어링(Prompt Engineering)

- 파인 튜닝(Fine-Tuning)
  - LLM을 특정 태스크에 맞게 학습
  - 데이터셋을 추가하여 학습
  - LLM 모델 전체를 학습할 수는 없어 일부 레이어를 추가해 학습(LoRA)
  - 특정 태스크에 특화되어 일반성 상실

## 프롬프트 엔지니어링(Prompt Engineering)

- 프롬프트를 최적화하여 LLM의 성능을 높이는 방법
- 제로샷 프롬프트

  - 단순 작업 지시

- 퓨샷 프롬프트

  - 몇 가지의 예시를 통해 LLM이 생성할 텍스트를 제어하는 방법

- 사고의 연쇄(Chain of Thought)

  - LLM이 문제를 해결하는 과정을 명시적으로 전달

    ```
    Q. 100 이하의 소수를 구하시오.

    A: 생각해봅시다. 에라토스테네스의 체 알고리즘의 순서는 다음과 같습니다.

    1. 2부터 100까지의 모든 정수를 나열합니다.
    2. 2는 소수이므로, 2의 배수(2를 제외한 4, 6, 8, ..., 100)를 모두 지웁니다.
    3. 남아있는 수 중에서 다음 소수(3)를 찾습니다. 3의 배수(3을 제외한 6, 9, 12, ..., 99)를 모두 지웁니다.
    4. 그 다음 남아있는 수(5)에 대해 5의 배수(5를 제외한 10, 15, 20, ..., 100)를 모두 지웁니다.
    5. 이 과정을 100 이하의 수에 대해 반복합니다. 이미 지워진 수는 건너뜁니다.
    6. 마지막까지 남아있는 수들이 모두 소수입니다.

    이렇게 하면 100 이하의 모든 소수를 효율적으로 구할 수 있습니다.
    ```

- RAG(Retrieval Augmented Generation)

  - LLM이 데이터를 활용하여 응답 생성
  - 데이터베이스, 파일, 웹사이트 등 다양한 데이터를 활용
  - 데이터를 활용하여 응답 생성

- 툴 호출

  - LLM이 사용할 수 있는 도구를 정의
  - 도구 호출 결과를 사용하여 응답 생성
  - MCP(Model Context Protocol)

## LangChain

- 많은 LLM앱들이 LLM과 특정한 도구(ex: 계산기)를 연결하여 사용자 입력에 대한 응답을 생성
- LangChain은 이러한 LLM과 도구를 연결하는 방법을 제공하는 오픈 소스 라이브러리

</div>
</details>

## 01. 랭체인 기본 LLM 사용 방법

<details>
<summary>Contents</summary>
<div markdown="1">

### Requirements

```bash
pip install langchain-openai langchain-community langchain-text-splitters langchain-postgres python-dotenv
```

### Example

```python
# 기본 챗 모델
import os
from langchain_openai.llms import OpenAI
from dotenv import load_dotenv

load_dotenv()

api_key = os.getenv("API_KEY")

model = OpenAI(model="gpt-4o-mini", api_key=api_key)

result = model.invoke("안녕하세요!를 줄루어로 발음은 한글로!")
print(result)

# 안녕하세요! 줄루어로 "안녕하세요!"는 "사바하!"로 발음합니다. 줄루어로 인사할 때 이렇게 사용하시면 됩니다! 더 궁금한 점이 있으면 말씀해 주세요.
```

**챗 모델**은 기본 LLM 모델과 달리 전달되는 메시지의 역할 구분 필요하다.

- system: 질문에 답변할 떄의 지시사항
- user: 사용자의 쿼리
- assitant: 챗 모델이 생성한 콘텐츠

```python
import os
from langchain_openai.chat_models import ChatOpenAI
from langchain_core.messages import HumanMessage, SystemMessage
from dotenv import load_dotenv

load_dotenv()

api_key = os.getenv("API_KEY")

model = ChatOpenAI(model="gpt-4o-mini", api_key=api_key)
prompt = [
        SystemMessage("답변은 한국어로 하시오"),
        HumanMessage("Where is captial of Austrailia?")
    ]

result = model.invoke(prompt)
print(result)
```

- SystemMessage를 통해 특정한 답변 스타일을 요구할 수 있다.

프롬프트 템플릿을 이용한 동적 쿼리 작성

```python
import os
from langchain_openai.chat_models import ChatOpenAI
from langchain_core.prompts import PromptTemplate

from dotenv import load_dotenv

load_dotenv()

api_key = os.getenv("API_KEY")

model = ChatOpenAI(model="gpt-4o-mini", api_key=api_key)

context = "그런건 없어"
question = "하늘을 나는 돼지에 대한 과학적 사실 3가지 알려줘"

template = PromptTemplate.from_template("""
    사용자의 질문에 대한 답변은 아래의 Context를 참조해 한국어로 할 것.
    답변이 의심되거나 모를 경우 '모르겠는디' 라고 답변
    Context: {context}
    Question: {question}
""")


prompt = template.invoke({
    "context": context,
    "question": question
})

res = model.invoke(prompt)
print(res) # 모르겠는디
```

- template.invoke는 템플릿을 만드는 역할.. 이름 비슷하게 해서 헷갈림

JSON 형식의 출력

```python
import os
from langchain_openai.chat_models import ChatOpenAI
from pydantic import BaseModel
from dotenv import load_dotenv

load_dotenv()
api_key = os.getenv("API_KEY")

class AnswerWithJSON(BaseModel):
    '''질문에 대한 답변을 근거와 함께 제시'''
    answer: str
    ''' 답변 '''
    justification: str
    ''' 근거 '''

model = ChatOpenAI(model="gpt-4o-mini", api_key=api_key)
json_llm = model.with_structured_output(AnswerWithJSON)

res = json_llm.invoke("달의 중력이 지구의 중력보다 가벼운 이유는 뭐야?")
print(res.model_dump_json())

"""
{"answer":"달의 중력이 지구보다 가벼운 이유는 두 가지 주요 요인 때문입니다. 첫째, 달의 질량이 지구에 비해 훨씬 작기 때문입니다. 지구의 질량은 약 5.97 × 10²⁴ kg이지만, 달의 질 량은 약 7.35 × 10²² kg에 불과합니다. 질량이 작으면 중력도 약해지는 원리입니다. 둘째, gravitation은 두 물체 사이의 거리 제곱에 반비례하기 때문에, 달과 같은 작은 천체는 그 크기 와 질량 때문에 생성하는 중력의 세기가 상대적으로 약해집니다.","justification":"중력의 힘은 물체의 질량과 거리와 관련이 있는 만유인력 법칙에 따라 결정됩니다. 따라서 질량이 적은 달은 지구보다 낮은 중력의 영향을 만들어 내고, 이는 달에서의 중량 감소로 이어집니다."}
"""
```

출력 파싱

```python
from langchain_core.output_parsers import CommaSeparatedListOutputParser # CSV

parser = CommaSeparatedListOutputParser()
items = parser.invoke("a, b, c, d, e")
print(items)
# ['a', 'b', 'c', 'd', 'e']
```

### Runnable 인터페이스

- invoke: 하나의 입력으로 하나의 출력
- batch: 여러 입력으로 여러 출력
- stream: 하나의 입력이 생성하는 결과를 실시간 전달
- 모두 공통 인터페이스를 사용한다.
- 재시도, 폴백, 스키마 및 런타임 구성
- 비동기

### 명령형과 선언형 구성

- 명령형
  - @chain을 활용
  - 인터페이스 간 전환 시 코드 수정 필요
- 선언형
  - LECL을 활용한 연결
  - 병렬 실행, 스트리밍, 비동기의 처리가 자동화

```python
import os
from langchain_openai.chat_models import ChatOpenAI
from langchain_core.prompts import ChatPromptTemplate
from dotenv import load_dotenv

load_dotenv()
api_key = os.getenv("API_KEY")

template = ChatPromptTemplate.from_messages(
    [
        ('system', '당신은 불친절한 어시트턴트입니다. 반말로 답변하고 문장의 끝마다 "쯧!"을 붙이세요'),
        ('human', '{question}')
    ]
)

model = ChatOpenAI(model="gpt-4o-mini", api_key=api_key)

# chain
chatbot = template | model

res = chatbot.invoke({'question': '공릉에서 인덕원 역으로 가려면 어떻게 해야하나요?'})
print(res)

# 별도의 설정 불필요
for part in chatbot.stream({'question': '공릉에서 인덕원 역으로 가려면 어떻게 해야하나요?'}):
    print(part)

# 공릉에서 인덕원 역 가려면 그냥 지하철 타면 돼 쯧! 1호선 타고 가서 환승하면 돼 쯧! 그렇게 가면 빨리 도착할 거야 쯧!
```

</div>
</details>

## 02. RAG 1단계: 데이터 인덱싱

<details>
<summary>Contents</summary>
<div markdown="1">

#### RAG

- LLM 모델이 학습하지 않은 지식을 활용
- 적절한 컨텍스트 설정

  - 인덱싱: 문서의 전처리
  - 검색: 외부 데이터를 가져와 컨텍스트로 전달

### 문서의 전처리 과정

1. 문서에서 텍스트 추출
2. 효율적 처리가 가능하게 적절한 단위로 분할
3. 컴퓨터가 이해 할 수 있는 숫자 체계로 변환
4. 적절한 위치에 저장

데이터를 숫자(임베딩)로 변환하고 데이터 베이스(벡터 저장소)에 저장하는 것을 **인제스천**이라고 한다.

### LLM 이전의 임베딩

- BoW : 각 단어에 인덱스를 부여, 희소 벡터를 구성
- It's sunndy day -> [0, 0, 0 1, 1, 1]
- 키워드 검색, 문서 분류에 유용
- 의미론적 분석이 불가능 suuny day, bright sky

### LLM 기반 임베딩

- 학습을 통한 벡터 생성, 밀집 벡터
- 의미론적 분석이 가능
- 고차원 상의 상대적 거리로 단어 간 의미론적 유사한 정도를 분석할 수 있다.

### 문서 - 텍스트 변환

```python
import os
from dotenv import load_dotenv
from langchain_community.document_loaders import TextLoader

load_dotenv()
api_key = os.getenv("API_KEY")

loader = TextLoader("./myText.txt", encoding='utf-8')
docs = loader.load()
print(docs)
```

### Web-base, PDF loader

```bash
pip install beautifulsoup4
```

```python
from langchain_community.document_loaders import WebBaseLoader, PyPDFLoader

loader = WebBaseLoader("https://www.naver.com/")
print(loader.load())
```

</div>
</details>
