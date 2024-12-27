# Langchain 이란?

### Langchain 이란?

**Langchain** 은 **대형 언어 모델(LLM)** 을 활용한 애플리케이션을 효과적으로 구축하기 위해 설계된 **Python 및 TypeScript 프레임워크임**



주로  GPT 계열의 모델과 같은 LLM을 중심으로 하는 다양한 자연어 처리(NLP) 응용 프로그램 개발을 지원함.

***

### 목표

1. **LLM 기반 애플리케이션의 체계적 설계 지원 :** 모델을 쉽게 조합하거나 통합하여 복잡한 워크플로우를 구성할 수 있음.
2. **외부 데이터와의 상호작용** : 데이터베이스, API 등 외부 데이터 소스와 상호작용하는 응용 프로그램 개발을 쉽게 함.
3. **기억, 맥락 및 에이전트 관리** : 애플리케이션의 기억 기능이나 유연한 사용자 인터페이스 제공

***

### 주요 구성 요소

1. Prompt Templates
   1. 역할 : LLM 에 전달되는 입력(프롬프트) 를 구성하고 관리
   2. 특징:
      1. 사용자 정의 프롬프트 템플릿 생성가능
      2. 다이나믹하게 입력값을 삽입.

```python
from langchain.prompts import PromptTemplate

template = "Translate the following text to French: {text}"
prompt = PromptTemplate(input_variables=["text"], template=template)
print(prompt.format(text="Hello, how are you?"))
# result : "Translate the following text to French: Hello, how are you?"
```



2. LLM Wrappers
   1. 역할 : 다양한 LLM(OpenAI GPT, Hugging Face 모델 등) 과 상호작용할 수 있는 인터페이스 제공.
   2. 지원 모델:
      1. OpenAI(GPT-3, GPT-4 등)
      2. Hugging Face Transformers
      3. Cohere, Anthropic, Google PaLM 등

```python
from langchain_openai import OpenAI

llm = OpenAI(model_name="text-davinci-003", temperature=0.7)
response = llm("What is the capital of France?")
print(response)
# result : "The capital of France is Paris."
```



3. Chains
   1. 역할 : 여러 작업을 연결하여 하나의 워크플로우로 구성
   2. 특징 :&#x20;
      1. LLM 호출, API 요청, 데이터 처리 등 다양한 작업을 연결 가능
      2. 단일 체인 또는 다단계 체인 구성

```python
from langchain.chains import LLMChain
from langchain.prompts import PromptTemplate
from langchain_openai import OpenAI

template = "What is a good name for a company that makes {product}?"
prompt = PromptTemplate(input_variables=["product"], template=template)
llm = OpenAI(model_name="text-davinci-003")
chain = LLMChain(llm=llm, prompt=prompt)

print(chain.run(product="smartphones"))
# result : "SmartTech Innovations"
```



4. Agents
   1. 역할 : 작업 흐름을 유연하게 처리하며, 외부 데이터나 도구에 접근
   2. 특징 :&#x20;
      1. 스스로 의사 결정을 내려 특정 작업을 수행
      2. ex) 데이터베이스 검색, 계산 수행, API 호출 등.

```python
from langchains.agents import load_tools, initialize_agent
from langchain_openai import OpenAI

llm = OpenAI(temperature=0)
tools = load_tools(["serpapi"]) # 검색 API 와 같은 도구 로드
agent = initialize_agent(tools, agent="zero-shot-react-description", verbose=True)

response = agent.run("What is the population of France?")
print(response)
```



5. Memory
   1. 역할 : 대화형 애플리케이션에서 상태(맥락)를 저장하고 관리
   2. 종류 :&#x20;
      1. **Short-term memory** : 최근 몇 대화만 저장.
      2. **Long-term memory** : 지속적인 대화 히스토리 관리.

```
from langchain.memory import ConversationBufferMemory
from langchain.chains import ConversationChain
from langchain_openai import OpenAI

memory = ConversationBufferMemory()
llm = OpenAI(temperature=0)
conversation = ConversationChain(llm=llm, memory=memory)

print(conversation.run("Hello!"))
print(conversation.run("What's my name?"))
```



6. Tools
   1. 역할 : 에이전트가 사용하는 외부 도구(API, 계산기 등)
   2. 사용 사례 :&#x20;
      1. 웹 스크래핑 도구
      2. 계산기
      3. 데이터베이스 쿼리



7. Vector Stores
   1. 역할 : 문서 검색과 같은 기능을 위해 문서 벡터화를 지원
   2. 통합 도구:
      1. Pinecone, Weaviate, FAISS 등



***

### Langchain 장점

1. 모듈화 : 각 구성 요소(Prompt, Chain 등) 를 독립적으로 사용하거나 조합 가능.
2. 확장성 : 다양한 LLM 및 데이터 소스와 손쉽게 통합.
3. 생산성 : LLM 기반 애플리케이션 개발 시간을 크게 단축
4. 오픈소스 : 누구나 무료로 사용 가능하며, 커뮤니티 지원이 활발함.

***

### 사례

1. 대화형 챗봇
   1. 사용자와 대화하면서 외부 API 나 데이터베이스에서 정보를 탐색
2. 문서 검색 시스템
   1. 텍스트를 벡터화하여 유사도를 기반으로 검색
3. 데이터 분석 및 처리
   1. 대규모 데이터 요약, 분석 보고서 생성
4. 자동화된 워크플로우
   1. 여러 작업을 연결한 자동화 프로세스 설계

