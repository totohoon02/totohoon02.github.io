# Chain of Thought

Chain of Thought(Cot)은 문제 해결 과정에서 중간 단계의 추론 과정을 명시적으로 드러내어, 복잡한 질문에 대해 더 정확하고 논리적인 답변을 유도하는 프롬프트 기법

> **Q. 삼식이네 네번째 아들 이름은 뭐야**

### 기본 프롬프트

```python
def chat(self, content):
    response = ollama.generate(
        model=os.getenv("CHAT_MODEL"),
        prompt=f"""
            질문에 답변

            ## 질문
            {content}
        """
    )
    return response["response"]
```

```
" 삼식이네 네번째 아들은 **[아래의 예시]로  해줄 수 있을까요?** 😊 \n\n\n\n**예시:** \n\n* **\"삼식의 네번째 아들, [아름다운 이름]** 입니다.\" \n* \"삼식의 네번째 아들이 **[특별한 이름]**입니다!\"\n\n\n어떤 스타일의 아이디어를 원하시는지 알려주세요! 😉 \n"
```

### CoT 프롬프트

```python
def chat(self, content):
    response = ollama.generate(
        model=os.getenv("CHAT_MODEL"),
        prompt=f"""
            질문에 답변

            1. 삼식이네 첫번째 아들 이름은 일식이야
            2. 삼식이네 두번째 아들 이름은 이식이야
            3. 삼식이네 세번째 아들 이름은 삼식이야
            ## 질문
            {content}
        """
    )
    return response["response"]
```

```
"삼식이네 네번째 아들은 **\"사식이\"**야! 😉 \n\n이는 문맥에 맞춰  '1. 삼식이네 첫번째 아들 이름은 일식이야', '2. 삼식이네 두번째 아들 이름은 이식이야', '3. 삼식이네 세번째 아들 이름은 삼식이야'를 기반으로 답변하는데, 문맥의 유연성을 활용하여 해석해야 합니다. \n"
```
