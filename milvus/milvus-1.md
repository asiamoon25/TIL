# Milvus 데이터 전처리 및 삽입

## 데이터 전처리란?

Milvus 에 데이터를 삽입하기 전에, 원시 데이터를 벡터화하고 필요한 필드를 정리하는 과정을 **데이터 전처리** 라고 함. 전처리는 데이터의 질과 검색 성능에 큰 영향을 미치며, Milvus 에서 벡터 검색을 활용하기 위한 필수 단계임.

***

## 전처리 과정

전처리 과정은 아래의 단계로 이루어짐.

### 1. 데이터 수집

* 데이터는 일반적으로 텍스트, 이미지, 오디오, 비디오 등 비정형 데이터로 구성됨.
* 예 : Markdown 파일에서 텍스트 추출

### 2. 데이터 정제

* 불필요한 HTML 태그, Markdown 형식 등을 제거하여 데이터를 클린업.
* 텍스트 정규화(공백 제거, 소문자 변환 등) 수행

### 3. 데이터 분할

* 텍스트를 일정한 크기의 Chunk 로 나눔.
* 예 : 1000 \~ 1500자 단위로 텍스트 분리

### 4. 벡터화(Embedding)

* 텍스트를 벡터로 변환하여 고차원 공간에서 표현
* 일반적으로 BERT, Sentence-BERT 또는 OpenAI Embedding 모델을 사용.

### 5.데이터 삽입

* Milvus 컬렉션에 벡터 및 메타데이터를 삽입.

***

## 데이터 전처리 구현

### 1. 데이터 정제

HTML 태그와 Markdown 형식을 제거하여 깨끗한 텍스트를 생성함.

```python
from bs4 import BeautifulSoup
import re

# HTML 및 Markdown 태그 제거 함수
def clean_text(text):
    # HTML 태그 제거
    soup = BeautifulSoup(text, "html.parser")
    text = soup.get_text()

    # Markdown 형식 제거 (예: #, *, [링크](URL))
    text = re.sub(r'[#*>\-]+', '', text)  # 제목, 목록 태그 제거
    text = re.sub(r'\[(.*?)\]\(.*?\)', r'\1', text)  # 링크 텍스트만 남기기
    text = re.sub(r'!\[.*?\]\(.*?\)', '', text)  # 이미지 제거
    text = re.sub(r'\s+', ' ', text).strip()  # 공백 정리
    return text

# 예제
markdown_content = """
# 제목
본문 내용입니다.
[링크](http://example.com)
"""
cleaned_content = clean_text(markdown_content)
print(cleaned_content)
```



### 2. 텍스트 분리

텍스트를 청크로 나눠 처리할 준비를 함.

```python
class TextSplitter:
    def __init__(self, chunk_size=1000, chunk_overlap=200):
        self.chunk_size = chunk_size
        self.chunk_overlap = chunk_overlap

    def split_text(self, text):
        chunks = []
        start = 0
        while start < len(text):
            end = min(start + self.chunk_size, len(text))
            chunks.append(text[start:end])
            start += self.chunk_size - self.chunk_overlap
        return chunks

# 예제
text_splitter = TextSplitter(chunk_size=1000, chunk_overlap=200)
chunks = text_splitter.split_text(cleaned_content)
print(chunks)
```

### 3. 벡터화

텍스트 청크를 벡터로 변환함.

```python
from sentence_transformers import SentenceTransformer

# BERT 모델 로드
model = SentenceTransformer("sentence-transformers/all-MiniLM-L6-v2")

# 청크 벡터화
embeddings = [model.encode(chunk) for chunk in chunks]
print(f"Generated {len(embeddings)} embeddings")
```

***

## 데이터 삽입

### Milvus 컬렉션 생성

Milvus 에 데이터를 삽입하기 위해 컬렉션을 생성함.

```python
from pymilvus import FieldSchema, CollectionSchema, DataType, Collection

# 컬렉션 스키마 정의
fields = [
    FieldSchema(name="id", dtype=DataType.INT64, is_primary=True, auto_id=True),
    FieldSchema(name="embedding", dtype=DataType.FLOAT_VECTOR, dim=384),  # 임베딩 차원
    FieldSchema(name="content", dtype=DataType.VARCHAR, max_length=3000),  # 텍스트 데이터
    FieldSchema(name="tag", dtype=DataType.VARCHAR, max_length=255),  # 태그 정보
]
schema = CollectionSchema(fields)

# 컬렉션 생성
collection = Collection(name="example_collection", schema=schema)
print("Collection created:", collection.name)
```



### 데이터 삽입

텍스트 청크, 벡터, 태그를 Milvus 에 삽입함.

```python
# 태그 설정
tag = "example_tag"

# Milvus에 데이터 삽입
data = {
    "embedding": embeddings,
    "content": chunks,
    "tag": [tag] * len(chunks),
}

collection.insert(data)
print(f"Inserted {len(chunks)} records into Milvus!")
```

***

## Milvus 에서 삽입된 데이터 확인

### 데이터 카운트

Milvus 에 삽입된 데이터 개수를 확인함.

```python
print("Total records:", collection.num_entities)
```



### 데이터 확인

컬렉션에서 삽입된 데이터를 조회함.

```python
# 첫 5개 데이터 조회
results = collection.query(
    expr="id >= 0",
    output_fields=["content", "tag"],
    limit=5
)
print("Sample data:", results)
```

