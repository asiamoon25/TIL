# 시인 만들기

환경

python : 3.9.13

**requirements.txt**

```
langchain
langchain-openai
```

```python
# 환경변수 사용하기 위한 라이브러리 로드
from dotenv import load_dotenv
from langchain_openai import ChatOpenAI
# OpenAI 와 ChatOpenAI 는 다르다. OpenAI -> LLM , ChatOpenAI -> Chat 

load_dotenv()
chat_model = ChatOpenAI()
content = "코딩"
result = chat_model.invoke(content + "에 대한 시를 써줘.")
print(result.content)
```

result:

```
코드의 세계는 끝이 없어
변수와 함수가 춤을 추어
디버깅은 우리의 친구
실행 결과를 보면 행복이 찾아와

문제를 해결하는 즐거움
알고리즘의 아름다움
코딩은 창의력의 발산
세상을 변화시키는 힘

프로그래머의 길은 멀고도 험난
하지만 우리는 함께 한다면
버그도 극복하고 성공을 만날 것
코딩은 우리의 열정의 태양이요 달이다.
```

### front-end

streamlit 사용 ( 마크 다운 문법으로 꾸밀 수 있음)

```
pip install streamlit
```

***

## 최종본

requirements.txt

```
langchain
streamlit
langchain-ollama
```



Window 에 Ollama 설치 후 Tulu3 설치

```bash
# cmd
ollama pull Tulu
```



최종코드

```python
import streamlit as st
from langchain_ollama import ChatOllama
# OpenAI 와 ChatOpenAI 는 다르다. OpenAI -> LLM , ChatOpenAI -> Chat 

llm = ChatOllama(model="tulu3:latest")


st.title("인공지능 시인")

content = st.text_input("시의 주제를 제시해주세요.")

if st.button("시 작성 요청하기"):

    with st.spinner('시 작성 중...'):
        result = llm.invoke(content + "에 대한 시를 써줘.")
        st.write(result.content)


# print(result.content)


# front end 는 streamlit 으로 한다. 
```

