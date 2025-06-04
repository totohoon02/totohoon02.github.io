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

### 텍스트를 분할하기

- 문서를 작은 단위로 분할하여 처리
- 청크 사이의 관련성을 유지하기 위해 chunk_overlap을 사용
- 코드의 경우 중복 불필요

```python
from langchain.text_splitter import RecursiveCharacterTextSplitter, Language
from langchain.document_loaders import TextLoader

loader = TextLoader("./myText.txt", encoding='utf-8')
docs = loader.load()

splitter = RecursiveCharacterTextSplitter(
    chunk_size=1000,
    chunk_overlap=200,
)

chunks = splitter.split_documents(docs)
print(chunks)

```

### 임베딩 생성

```python
from langchain.embeddings import OpenAIEmbeddings

embeddings = OpenAIEmbeddings()

print(embeddings.embed_query("Hello world"))
print(embeddings.embed_documents(["Hello world", "Hello world 2"]))

```

### 데이터베이스에 저장

```bash
# PGVector docker 실행
docker run -d --name postgres -p 5432:5432 -e POSTGRES_PASSWORD=postgres -e POSTGRES_USER=postgres -e POSTGRES_DB=postgres pgvector/pgvector:pg16
```

```python
from langchain_community.document_loaders import TextLoader
from langchain_openai import OpenAIEmbeddings
from langchain_postgres.vectorstores import PGVector
from langchain.text_splitter import RecursiveCharacterTextSplitter

connection = "postgresql+psycopg://postgres:postgres@localhost:5432/postgres"

# load document
loader = TextLoader("./myText.txt", encoding='utf-8')
docs = loader.load()

# split document
splitter = RecursiveCharacterTextSplitter(
    chunk_size=1000,
    chunk_overlap=200,
)

chunks = splitter.split_documents(docs)

# create embedding
embeddings = OpenAIEmbeddings()

db = PGVector.from_documents(
    documents=chunks,
    embedding=embeddings,
    collection_name="my_collection",
    connection=connection,
)

# 문서 추가
db.add_documents(
	Document(page_content="Hello")
)

# 검색
result = db.similarity_search("Hello")
print(result)

# 변경사항 추적
from langchain.indexes import index

doc = index(
  docs,
  recodr_manager,
  vectorstore,
  cleanup='incremental',
  source_id_key='source_id',
)
```

문서 로드 -> 텍스트 분할 -> 임베딩 생성 -> 데이터베이스에 저장

- source_id_key를 이용해 문서의 변경사항을 추적한다.

### 인덱싱 최적화

- 데이터에 이미지, 표 등이 포함된 경우 검색 결과의 일관성이 떨어질 수 있다.
- 성능 향상을 위한 최적화 전략

1. MultiVectorRetriever

- 요약한 내용을 임베딩으로 사용
- 전체 문서를 문서 저장소에 저장, 임베딩에서 문서의 id를 참조
- LLM에 전체 컨텍스트를 제공

2. RAPTOR

- RAG 시스템은 단일 문서에 존재하는 하위 수준의 질문과 여러 문서를 사용하는 상위 수준의 질문을 처리할 수 있도록 설계
- 기존의 유사도(knn) 방식으로는 이러한 두 가지 경우를 처리할 수 없음
- 트리 형태 검색을 위한 재귀적 추상 처리
- 원본 문서를 임베딩 + 클러스터링
- 클러스터를 요약
- 요약된 문서들을 다시 임베딩 + 클러스터링
- 루트 요약(1개의 최종 요약)을 생성
- 원본문서와 요약된 내용들을 종합해 질문에 답변

```
       [Root 요약]
         /    \
     [중간 요약] ...
      /     \
[청크1] [청크2] ...

```

3. ColBERT

- 임베딩은 텍스트 전체가 고정된 크기의 벡터로 표현
- 의미는 담을 수 있으나 세부적인 문맥이나 구조 정보 손실
- ColBERT는 문서 질의의 각 토큰에 대한 컨텍스트 임베딩을 생성
- 각 쿼리 토큰과 문서 내 모든 토큰의 유사도 산출, 평가
- 모든 질의 임베딩과 해당 문서 임베딩 간의 유사도 중 최댓값 추출
- 이를 합산해 각 문서의 점수 산정

```
🔍 쿼리: ["고양이", "먹이"]
📄 문서1: ["고양이는", "생선을", "좋아한다"]
📄 문서2: ["강아지는", "사료를", "먹는다"]

→ "고양이"는 문서1에서 "고양이는"과 유사
→ "먹이"는 문서1에서 "생선"과 유사

→ 문서1은 쿼리와 잘 매칭됨 → 높은 점수

→ 문서2는 두 단어 모두 관련 없음 → 낮은 점수
```

</div>
</details>

## 03. RAG 2단계: 데이터 기반 대화

<details>
<summary>Contents</summary>
<div markdown="1">

### 쿼리 변환

- 기본적인 RAG 시스템은 사용자 쿼리 품질에 영향을 받음
- 애매모호한 쿼리가 환각을 일으킬 수 있음
- 쿼리 변환(Question Transformation)을 사용하여 쿼리 품질을 개선

#### 재작성-검색-읽기

- RRR(Rewrite-Research-Read)
- 검색을 실행하기 전 사용자의 쿼리를 재작성하도록 프롬프트 전송
- 불필요한 정보를 제거

```
주어진 질문에 답할 수 있도록 더 나은 영문 검색어를 제공하세요.
```

- LLM을 두번 호출해야 하기 때문에 추가적인 지연 시간이 발생

#### 다중 쿼리 검색

- 초기 쿼리를 바탕으로 여러 개의 쿼리를 생성
- 각 쿼리에 대해서 병렬 실행
- 생성된 컨텍스트들을 활용해 최종 답변 생성

### RAG 융합

#### 상호 순위 융합(RRF)

- 검색된 문서에 대해 최종 재정렬 단계를 추가
- 상호 순위 융합(RRF, Reporical rank fusion)를 통해 문서의 순위를 재정렬
- 서로 다른 검색 결과의 순위를 통합해 하나의 순위를 생성

```python
def easy_rrf(result_lists, k=60):
    scores = {}     # 각 문서의 총 점수를 저장할 딕셔너리

    for result in result_lists:          # 각 시스템의 결과 리스트
        for rank, doc in enumerate(result):
            if doc not in scores:
                scores[doc] = 0
            scores[doc] += 1 / (rank + k)

    # 점수가 높은 순으로 정렬
    sorted_docs = sorted(scores.items(), key=lambda x: x[1], reverse=True)

    # 문서 이름만 리스트로 반환
    return [doc for doc, _ in sorted_docs]

retrieval_chain = query_gen | retriever.batch | easy_rrf
```

여러 검색 시스템이 있고 같은 질문에 대해 각기 다른 결과를 생성한다고 가정한다. 예를 들어

> “한국의 수도는?”

시스템 A 결과:

- 서울
- 부산
- 인천

시스템 B 결과:

- 부산
- 서울
- 대구

시스템 C 결과:

- 서울
- 대전
- 부산

> 어떤 답이 가장 공통적으로 좋다고 판단했는지를 반영한 최종 리스트를 생성

서울의 점수

- 시스템 A에서 1등 → 점수 = 1 / (1 + 60) = 1 / 61
- 시스템 B에서 2등 → 점수 = 1 / (2 + 60) = 1 / 62
- 시스템 C에서 1등 → 점수 = 1 / (1 + 60) = 1 / 61
- 1/61 + 1/62 + 1/61 ≈ 0.0164 + 0.0161 + 0.0164 = 약 0.0489

> 상위권에 자주 등장하는 문서가 더 높은 점수를 받게 된다.<br> **공통적으로 좋다**

#### 가상 문서 임베딩

- 사용자의 쿼리를 토대로 가상의 문서 작성, 임베딩
- 가상의 문서가 원래의 쿼리보다 찾으려는 문서와 관련성이 더 높을 것이라고 가정

### 쿼리 라우팅

- 쿼리를 적절한 데이터소스로 전달하는 방법

#### 논리적 라우팅

- 사용자의 표현된 키워드나 패턴 등 표면적 정보를 기반으로 분기
- "2+2는 얼마야?" -> 숫자, + ->수학 데이터소스로 전달

```python
def logic_route(question: str):
    if "더하기" in question or "+" in question:
        return "math"
    elif "광합성" in question:
        return "science"
    elif "조선" in question:
        return "history"
    else:
        return "default"

```

#### 의미론적 라우팅

- 질문의 의미를 LLM이 해석, 라우팅
- "물이 끓는 온도는?" -> 끓는다, 온도 -> 과학 개념

```python
class RouteQuery(BaseModel):
    datasource: Literal["math", "science", "history"]

# system prompt 예시
system_prompt = """
당신은 질문을 분석해서 어떤 데이터소스가 가장 적절한지 판단합니다.
가능한 선택지는 다음과 같습니다: math, science, history.
"""

# prompt + LLM = 의미 해석 기반 라우팅
router_chain = ChatPromptTemplate.from_messages([
    ("system", system_prompt),
    ("human", "{question}")
]) | ChatOpenAI(...).with_structured_output(RouteQuery)

```

### 쿼리 구성

- 임베딩된 비정형 데이터에 대한 정보를 담은 정형 메타데이터를 포함

#### 텍스트-메타데이터 필터

```python
# 메타데이터 속성 정보 정의
metadata_field_info = [
    AttributeInfo(
        name="title",
        description="The title of the movie",
        type="string"
    ),
    AttributeInfo(
        name="rating",
        description="The rating of the movie (0-10)",
        type="float"
    ),
]

# SelfQueryRetriever 생성
retriever = SelfQueryRetriever.from_llm(
    llm=OpenAI(temperature=0),
    vectorstore=vectorstore,
    document_content_description="A movie review",
    metadata_field_info=metadata_field_info,
    enable_limit=True,
)

# 쿼리 예시: "평점이 8.0 이상인 스릴러 영화 찾아줘"
results = retriever.invoke("평점이 8.0 이상인 스릴러 영화 찾아줘")

for doc in results:
    print(doc.page_content, doc.metadata)
```

#### 텍스트-SQL 변환

- 데이터베이스 설명
- 퓨샷 예시

```python
from langchain.tools.sql_database.tool import QuerySQLDatabaseTool
from langchain.sql_database import SQLDatabase
from langchain.llms import OpenAI

# SQLite 데이터베이스 연결
db = SQLDatabase.from_uri("sqlite:///movies.db")

# LLM 준비
llm = OpenAI(temperature=0)

# 질문을 sql로 변환
write_query = create_sql_query_chain(llm, db)

# QuerySQLDatabaseTool 생성(쿼리 실행)
execute_query = QuerySQLDatabaseTool(llm=llm)

# 자연어로 쿼리 실행
query = "평점이 8.0 이상인 스릴러 영화의 제목과 평점을 알려줘"

combined_chain = write_query | execute_query

result = combined_chain.invoke(query)

print(result)
```

</div>
</details>

## 04. 랭그래프를 활용한 메모리 기능

<details open>
<summary>Contents</summary>
<div markdown="1">

> Hello!

</div>
</details>

<!-- ## RAG -->

<!-- <details> -->
<!-- <summary>Contents</summary> -->
<!-- <div markdown="1"> -->

<!-- </div> -->
<!-- </details> -->
