# 로드맵

#### **1. LangChain 기본 이해**

**목표**

* LangChain의 개념과 주요 구성 요소 이해.
* 설치 및 간단한 코드 실행.

**학습 및 실습**

1. **LangChain의 개념**
   * LLM 기반 파이프라인을 설계하고 실행하기 위한 프레임워크.
   * 주요 구성 요소:
     * **PromptTemplates**: 사용자 정의 프롬프트 생성.
     * **Chains**: 작업 흐름을 연결하여 여러 LLM 호출 처리.
     * **Memory**: 대화 기록을 저장하여 컨텍스트를 유지.
     * **Agents**: 동적 작업 흐름.
     * **Tools**: 외부 API 호출 등 다양한 도구 통합.
2.  **LangChain 설치**

    ```bash
    pip install langchain openai
    ```
3.  **간단한 LangChain 실행**

    ```python
    from langchain.prompts import PromptTemplate
    from langchain.llms import OpenAI

    # 프롬프트 정의
    prompt = PromptTemplate(
        input_variables=["name"],
        template="Hello, {name}! How can I assist you today?"
    )

    # LLM 초기화
    llm = OpenAI(model="text-davinci-003")

    # 실행
    print(llm(prompt.format(name="Alice")))
    ```

***

#### **2. LangChain 주요 구성 요소 학습**

**목표**

* LangChain의 각 구성 요소를 상세히 이해하고 활용 방법 익히기.

**학습 및 실습**

1. **PromptTemplates**
   * 사용자 입력을 기반으로 템플릿화된 프롬프트 생성.
   * **TIL 작성**: PromptTemplate 사용법과 실습 코드 기록.
2.  **Chains**

    * 여러 작업을 순차적으로 실행하는 체인 구성.
    * 예: 텍스트 생성 → 요약 → 번역.

    ```python
    from langchain.chains import SimpleSequentialChain
    from langchain.llms import OpenAI

    # LLMs
    llm1 = OpenAI(model="text-davinci-003")
    llm2 = OpenAI(model="text-davinci-003")

    # 체인 구성
    chain = SimpleSequentialChain(
        chains=[
            llm1.as_chain(),
            llm2.as_chain()
        ]
    )

    result = chain.run("LangChain 활용법 학습")
    print(result)
    ```
3.  **Memory**

    * 대화형 AI에 컨텍스트 유지 추가.
    * 예: 챗봇이 이전 메시지를 기억.

    ```python
    from langchain.chains import ConversationChain
    from langchain.memory import ConversationBufferMemory

    memory = ConversationBufferMemory()
    chain = ConversationChain(llm=llm, memory=memory)

    print(chain.run("What is LangChain?"))
    print(chain.run("How does it handle memory?"))
    ```
4.  **Agents and Tools**

    * **Agents**: 동적으로 작업 흐름을 처리.
    * **Tools**: 외부 API와 통합.

    ```python
    from langchain.agents import initialize_agent, load_tools
    from langchain.llms import OpenAI

    tools = load_tools(["google-search", "calculator"])
    agent = initialize_agent(tools, llm, agent="zero-shot-react-description", verbose=True)

    result = agent.run("What is the capital of France plus 100?")
    print(result)
    ```

***

#### **3. LangChain 데이터 연결**

**목표**

* LangChain을 데이터베이스, 파일, API와 연결.
* 예: Milvus, SQL, JSON 파일 등.

**학습 및 실습**

1.  **데이터 연결**

    * Milvus를 활용한 벡터 검색.

    ```python
    from langchain.vectorstores import Milvus
    vectorstore = Milvus(host="localhost", port="19530")

    result = vectorstore.search("LangChain 활용법", k=5)
    print(result)
    ```
2.  **SQL 연결**

    * SQL 데이터베이스와 연결하여 쿼리 실행.

    ```python
    from langchain.sql_database import SQLDatabase
    from langchain.chains import SQLDatabaseChain

    db = SQLDatabase.from_uri("sqlite:///example.db")
    chain = SQLDatabaseChain(llm=llm, database=db)

    print(chain.run("List all users with their emails"))
    ```
3.  **파일 연결**

    * PDF, JSON 등 파일 데이터 연결.

    ```python
    from langchain.document_loaders import PyPDFLoader

    loader = PyPDFLoader("example.pdf")
    documents = loader.load()
    print(documents[0].page_content)
    ```

***

#### **4. LangChain 활용 사례**

**목표**

* 실제 사용 사례를 실습하여 TIL에 기록.
* 예: 챗봇, 추천 시스템, 자동화 작업.

**학습 및 실습**

1.  **챗봇 구현**

    * 대화형 메모리를 추가하여 챗봇 구축.

    ```python
    from langchain.chains import ConversationChain

    memory = ConversationBufferMemory()
    chatbot = ConversationChain(llm=llm, memory=memory)

    print(chatbot.run("Tell me about LangChain."))
    print(chatbot.run("How does it differ from other frameworks?"))
    ```
2.  **추천 시스템**

    * 사용자의 입력에 따라 관련 문서를 검색하고 추천.

    ```python
    result = vectorstore.search("추천 시스템 만들기", k=3)
    print(result)
    ```
3.  **API 자동화**

    * Agents와 Tools를 활용해 Google 검색, 계산기 등 자동화.

    ```python
    result = agent.run("Calculate the population of France times 2.")
    print(result)
    ```

***

#### **5. LangChain 최적화**

**목표**

* 성능 및 사용성을 향상시키기 위한 최적화 방법 학습.
* 예: 프롬프트 최적화, 캐싱, 외부 툴 통합.

**학습 및 실습**

1.  **프롬프트 최적화**

    * 프롬프트 설계의 중요성 학습 및 최적화 실습.

    ```python
    prompt = PromptTemplate(
        input_variables=["question"],
        template="You are an expert in AI. Please answer: {question}"
    )
    ```
2.  **결과 캐싱**

    * 동일한 요청에 대한 재응답 시간을 줄이기 위해 캐싱 사용.

    ```python
    from langchain.cache import InMemoryCache

    cache = InMemoryCache()
    llm.cache = cache
    ```
3.  **사용자 정의 도구 통합**

    * 새로운 툴 생성 및 LangChain에 통합.

    ```python
    from langchain.tools import BaseTool

    class CustomTool(BaseTool):
        name = "custom_tool"
        description = "A custom tool for specific tasks."

        def _run(self, query: str):
            return f"Processed: {query}"

    tool = CustomTool()
    ```

***

####
