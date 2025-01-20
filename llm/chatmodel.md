# ChatModel 이해 및 구현

## 1. 개념

### 특징

#### 1. 문맥 유지

* 대화 내역(컨텍스트)을 기반으로 사용자 입력에 맞는 적절한 응답 생성
* 여러 턴(Turn) 동안의 대화 흐름을 이해

#### 2. 사전 학습

* 대규모 대화형 데이터셋으로 학습.

#### 3. 미세 조정

* 특정 태스크(예: 고객 서비스) 에 맞춰 미세 조정

### 작동 원리

#### 입력

* 대화 내역(\`messages\`) 및 사용자 메세지
* 예: "Tell me about LangChain"

#### 출력

* 입력을 기반으로 생성된 모델의 응답.
* 예: "Langchain is a framework for building LLM applications."

#### 컨텍스트 관리

* 대화 중 이전 메시지를 기반으로 응답&#x20;

***

## 2. ChatModel 의 주요 구성 요소

### 1. 대화 컨텍스트

* 대화 내역(`messages`) 을 저장하고 이를 모델 입력으로 사용

### 2. 역할(Role)

* 메시지는 일반적으로 `system`, `user`, `assistant` 역할로 구분
  * `system`: 모델ㅇ의 행동과 성격을 정의
  * `user`: 사용자의 입력 메시지.
  * `assistant` : 모델이 생성한 응답.

### 3. 토큰 관리

* 대화 내역이 길어질 경우 토큰 제한에 도달할 수 있으므로 중요 메시지만 유지하거나 요약 필요

***

## 3. ChatModel 구현 방법

### 1. OpenAI API 를 활용한 ChatModel

OpenAI 의 ChatGPT API 는 대화형 모델을 간단히 활용할 수 있는 도구를 제공함.

```python
import openai

# API 키 설정
openai.api_key = "your-api-key"

# 대화 내역 정의
messages = [
    {"role": "system", "content": "You are a helpful assistant."},
    {"role": "user", "content": "What is LangChain?"}
]

# ChatModel 호출
response = openai.ChatCompletion.create(
    model="gpt-3.5-turbo",
    messages=messages
)

# 응답 출력
print(response['choices'][0]['message']['content'])
```



### 2. 대화 컨텍스트 관리

ChatModel 은 대화내역(`messages`) 을 기반으로 작동함. 따라서, 이전 메시지를 저장하고 유지해야함.

```python
messages.append({"role": "assistant", "content": "LangChain is a framework for building applications using LLMs."})
messages.append({"role": "user", "content": "Can you explain it in detail?"})

response = openai.ChatCompletion.create(
    model="gpt-3.5-turbo",
    messages=messages
)

print(response['choices'][0]['message']['content'])

```



### 3. LangChain 을 활용한 ChatModel

LangChain 은 ChatModel 을 효율적으로 관리하고, 대화 컨텍스트를 유지하는 데 도움을 줌.

**LangChain 예제**

```python
from langchain.chains import ConversationChain
from langchain.memory import ConversationBufferMemory
from langchain.llms import OpenAI

# 메모리 초기화
memory = ConversationBufferMemory()

# LangChain ConversationChain 구성
llm = OpenAI(model="gpt-3.5-turbo")
conversation = ConversationChain(llm=llm, memory=memory)

# 대화
print(conversation.run("What is LangChain?"))
print(conversation.run("How does it manage memory?"))

```

### 4. Hugging Face 를 사용한 ChatModel

Hugging Face 는 오픈소스 ChatModel 을 사용할 수 있는 플랫폼을 제공함.

```python
from transformers import AutoTokenizer, AutoModelForCausalLM

# 모델 및 토크나이저 로드
model_name = "microsoft/DialoGPT-medium"
tokenizer = AutoTokenizer.from_pretrained(model_name)
model = AutoModelForCausalLM.from_pretrained(model_name)

# 대화 입력
input_text = "What is LangChain?"
inputs = tokenizer.encode(input_text, return_tensors="pt")

# 모델 실행 및 응답 디코딩
outputs = model.generate(inputs, max_length=50)
print(tokenizer.decode(outputs[0]))
```

