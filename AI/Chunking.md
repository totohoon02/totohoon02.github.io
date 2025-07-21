# Chunking

## Fixed-Size

- 고정 크기로 분할
- 처리가 쉬우나 문맥의 손실 가능성

## Semantic

- 의미있는 단위로 분할
- 예를 들어 문단 단위로 분할
- 맥락 유지, 길이가 불균형, 구현 복잡

## Slide

- 오버랩된 청크
- 토큰을 0 ~ 512, 256 ~ 768
- 문맥 유지, **계산량 증가**

## Recursive

- 문장 > 문단 > 구문으로 계층적 분할
- LangChain의 `RecursiveCharacterTextSplitter`
- 너무 짧거나 긴 문단을 적절히 다듬는다

```python
text_splitter = RecursiveCharacterTextSplitter(
    chunk_size=100,           # 최대 청크 크기 (문자 단위 or 토큰 단위 depending on tokenizer 설정)
    chunk_overlap=20,         # 겹치는 길이
    separators=["\n\n", "\n", ".", " ", ""],  # 의미 단위로 자르기 위한 우선순위 구분자
)
```

## Toekn-aware

- 최대 토큰 수를 기준으로 문장이 끊기지 않게 분할
- HugginFace, LlamaIndex

### 비교

| 상황                | 추천 청킹 방식            |
| ------------------- | ------------------------- |
| LLM 임베딩/RAG      | Fixed-size + Sliding      |
| 자연스러운 질의응답 | Semantic, Recursive       |
| 긴 문서 요약        | Token-aware or Recursive  |
| 코드 분석           | 함수 단위 청킹 (Semantic) |
