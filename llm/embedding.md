# Embedding 의 이해 및 활용

## 1. Embedding 이란?&#x20;

Embedding 은 텍스트, 이미지, 오디오 등의 데이터를 **고차원 벡터 공간** 으로 변환하는 과정임.

Embedding 은 기계 학습과 자연어 처리(NLP) 에서 데이터 간의 관계를 숫자로 표현하여 유사도 계산, 검색, 추천 등의 작업을 가능하게 함.

***

## 2. Embedding 작동 원리

**벡터 표현(Vector REpresentation)**

Embedding 은 데이터를 고차원 벡터(숫자 배열) 로 변환함. 이 벡터는 데이터의 의미나 특징을 반영하며, 벡터 간의 거리는 데이터 간의 관계를 나타냄.

* 유사도 계산
  * 벡터 간 거리가 가까울수록 데이터 간의 의미가 유사
  * 주요 거리 측정 방식:
    * Cosine Similarity : 두 벡터의 각 유사도
    * Euclidean Distance(L2) : 벡터 간 직선 거리

***

## 3. Embedding 의 주요 활용 사례

### 1. 유사도 검색

* 입력 텍스트와 데이터베이스의 벡터 간 유사도를 계산하여 관련 데이터를 검색.
* 예:  "LangChain 이 무엇인가?" 를 검색하여 관련 문서를 반환.



### 2. 추천 시스템

* 사용자와 아이템의 벡터를 비교하여 개인화된 추천 제공
* 예 : 사용자가 좋아할 영화를 추천



### 3. 군집화(Clustering)

* 데이터 벡터를 클러스터링하여 비슷한 그룹을 형성
* 예 : 고객 세분화



### 4. 분류(Classification)

* 벡터를 기반으로 텍스트 또는 데이터 분류
* 예: 이메일을 스팸과 일반 메일로 분류



### 5. 검색 엔진 최적화

* 입력 문장을 Embedding 으로 변환하여 가장 관련성 높은 결과를 반환.

***

## 4. Embedding 생성 방법

### 1. Pre-trained Embedding 모델

사전 학습된 모델을 사용하여 데이터를 Embedding 으로 변환.

**OpenAI Embedding API**

OpenAI 의 `text-embedding-ada-002` 모델은 고성능 Embedding 을 생성함.

```python
import openai

openai.api_key = "your-api-key"

response = openai.Embedding.create(
    input=["What is LangChain?", "What is GPT?"],
    model="text-embedding-ada-002"
)

embeddings = response['data']
print(embeddings[0]['embedding'])  # 첫 번째 문장의 벡터
```



**Hugging Face SentenceTransformer**

Hugging Face 의 SentenceTransformer 를 사용하여 Embedding 생성.

```python
from sentence_transformers import SentenceTransformer

model = SentenceTransformer('all-MiniLM-L6-v2')
sentences = ["What is LangChain?", "What is GPT?"]

embeddings = model.encode(sentences)
print(embeddings.shape)  # 벡터 크기
```



### 2. 사용자 정의 Embedding 모델

사용자 정의 데이터셋으로 Embedding 모델을 미세 조정(Fine-Tuning) 하여 특정 도메인에 최적화.

* 방법:
  * Hugging Face 모델 로드
  * 사용자 정의 데이터로 학습.

***

## 5. Embedding 활용 사례

### 1. 유사도 계산

텍스트 벡터 간의 유사도를 계산

```python
from sklearn.metrics.pairwise import cosine_similarity

similarity = cosine_similarity([embeddings[0]], [embeddings[1]])
print(f"Similarity: {similarity[0][0]:.4f}")
```



### 2. 벡터 검색

Milvus, Pinecone, Weaviate 같은 벡터 데이터베이스를 사용하여 Embedding 기반 검색 구현

```python
from langchain.vectorstores import Milvus

# Milvus에 연결
vectorstore = Milvus(host="localhost", port="19530")
vectorstore.add_texts(["What is LangChain?", "What is GPT?"], embeddings)

# 검색
query_embedding = model.encode("Explain LangChain")
results = vectorstore.similarity_search(query_embedding, k=1)
print(results)
```



### 3. 추천 시스템

* 사용자의 행동 데이터를 Embedding 으로 변환하고, 유사도가 높은 아이템 추천

