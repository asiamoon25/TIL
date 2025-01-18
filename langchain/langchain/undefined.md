# 개념

## Langchain 이란?

Langchain 은 **LLM** 을 활용한 애플리케이션을 쉽게 구축하고 확장할 수 있도록 설계된 **프레임 워크** 임. Langchain 은 LLM 의 강력한 언어 생성 능력을 최대한 활용하기 위해 다음과 같은 주요 기능을 제공함.

* **프롬프트 템플릿** 관리
* **체인(chains)** 로 대화 컨텍스트 유지
* **외부 데이터와의 통합** (데이터베이스, API)
* **Agent** 를 사용한 동적 작업 처리

Langchain 은 다양한 구성 요소를 연결하여 LLM 기반 애플리케이션의 생산성과 확장성을 극대화함.



***

## 사용 이유

### 문제점

LLM 을 직접 사용하는 경우:

1. 복잡한 작업 흐름 관리가 어려움.
2. 대화형 AI 에서 컨텍스트 유지를 수동으로 구현해야함.
3. 데이터베이스나 외부 툴과의 통합 작업이 번거로움.

#### Langchain의 해결책

Langchain 은 LLM 의 다양한 활용 방식을 체계적으로 관리하며, 다음과 같은 이점을 제공함.

1. 간단한 워크플로우 설계
   1. 복잡한 작업을 체인으로 연결하여 효율적으로 실행.
2. 컨텍스트 관리
   1. 메모리 기능을 활용해 대화 컨텍스트를 유지
3. 통합성
   1. 데이터베이스, 벡터 스토어, API 등 외부 도구와 쉽게 연동가능
4. 확장성
   1. 에이전트를 사용해 동적이고 복잡한 작업을 처리

***

## Langchain 의 주요 구성 요소

Langchain 은 **모듈화** 된 구조로 설계되었으며, 각 구성 요소는 독립적으로 사용할 수 있음. 주요 구성 요소는 다음과 같음.



### 1. Prompts

프롬프트 템플릿을 사용하여 LLM에 전달할 입력을 동적으로 생성함.

* **PromptTemplate**
  * 입력 변수를 기반으로 프롬프트 생성.

```python
from langchain.prompts import PromptTemplate

template = PromptTemplate(
    input_variables=["name"],
    template="Hello {name}, how can I assist you today?"
)
print(template.format(name="Alice"))  # 출력: Hello Alice, how can I assist you today?
```



### 2. Chains

여러 작업을 순차적으로 연결하는 워크플로우를 만듬.

* **SimpleSequentialChain**
  * 여러 LLM 호출을 순서대로 연결

```python
from langchain.chains import SimpleSequentialChain

chain = SimpleSequentialChain(chains=[step1, step2])
result = chain.run("Input Data")
```

* **TransformChain**
  * 데이터를 변환하거나 정리하는 로직 포함.

### 3. Memory

대화형 애플리케이션에서 컨텍스트를 유지하기 위해 사용됨.

* **ConversationBufferMemory**
  * 이전 대화를 저장하여 컨텍스트를 유지

```python
from langchain.memory import ConversationBufferMemory

memory = ConversationBufferMemory()
memory.save_context({"input": "Hello"}, {"output": "Hi! How can I help you?"})
print(memory.load_memory_variables({}))
```



### 4. Tools

외부 도구(API, 계산기, 데이터베이스 등) 를 호출할 수 있도록 통합함.

* 예 : Google 검색, 계산기

```python
from langchain.tools import Tool

tool = Tool(name="calculator", func=lambda x: eval(x), description="Simple calculator tool")
print(tool.run("2 + 2"))  # 출력: 4
```



### 5. Agents

에이전트를 사용해 동적으로 작업을 수행하며, Tools 를 활용해 복잡한 작업을 처리함.

* **Zero-shot Agent**
  * 주어진 작업을 Tools 를 사용해 해결

```python
from langchain.agents import initialize_agent, load_tools

tools = load_tools(["google-search", "calculator"])
agent = initialize_agent(tools, llm, agent="zero-shot-react-description", verbose=True)

print(agent.run("What is the capital of France plus 100?"))
```



### 6. Data Connection

Langchain 은 벡터 스토어, SQL 데이터베이스, PDF 파일 등 외부 데이터를 쉽게 연결할 수 있도록 도와줌.

* 벡터 스토어 연결

```python
from langchain.vectorstores import Milvus

vectorstore = Milvus(host="localhost", port="19530")
```

* SQL 연결

```python
from langchain.sql_database import SQLDatabase

db = SQLDatabase.from_uri("sqlite:///example.db")
```



### 7. LLMs

Langchain 은 OpenAI, Hugging Face 등 다양한 LLM 을 지원함.

***

## Langchain 의 주요 사용 사례

### 1. 대화형 챗봇

* Memory 를 사용해 컨텍스트를 유지하는 대화형 애플리케이션 구축

```python
from langchain.chains import ConversationChain
from langchain.memory import ConversationBufferMemory

memory = ConversationBufferMemory()
chatbot = ConversationChain(llm=llm, memory=memory)

print(chatbot.run("Tell me about LangChain."))
print(chatbot.run("How does it handle memory?"))
```



### 2. 검색 및 요약

* Langchain 과 벡터 데이터베이스를 결합하여 문서를 검색하고 요약

```python
from langchain.chains import RetrievalQA
from langchain.vectorstores import Milvus

vectorstore = Milvus(host="localhost", port="19530")
retriever = vectorstore.as_retriever()
chain = RetrievalQA(llm=llm, retriever=retriever)

result = chain.run("What is LangChain?")
print(result)
```



### 3. 멀티모달 애플리케이션

* 텍스트, 이미지, API 등을 결합한 복합 애플리케이션

***

## 장점

### 1. 모듈화

* Prompt, Chains, Memory 등 각 모듈을 독립적으로 사용 가능

### 2. 확장성

* 다양한 외부 도구 및 데이터 소스와 손쉽게 통합.

### 3. 생산성 향상

* 복잡한 작업 흐름을 단순화
