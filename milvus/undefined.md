---
description: 임베딩과 벡터 DB 를 이용한 검색 시스템
---

# 로드맵

#### **로드맵 개요**

**주제: Milvus 기반 벡터 검색 시스템 구축 및 데이터 전처리**

1. **벡터 DB란?**
2. **Milvus 기본 이해 및 환경 설정**
3. **데이터 전처리 및 삽입**
4. **효율적인 검색 로직 구현**
5. **추가 최적화 및 기능 확장**
6. **Markdown 기반 TIL 정리 템플릿**

***

#### **1단계: Milvus 기본 이해 및 환경 설정**

**학습 목표:**

* Milvus의 기본 개념과 설치 방법을 이해.
* Milvus 클라이언트와 컬렉션 생성 및 설정 방법 익히기.

**TIL 작성 항목:**

1. **Milvus란 무엇인가?**
   * 벡터 데이터베이스의 개념.
   * Milvus의 주요 특징(벡터 검색, 고성능, 확장성).
2. **Milvus 설치 및 환경 설정**
   * Docker 기반 Milvus 설치 명령어.
   * Python SDK 설치 및 간단한 연결 테스트 코드.

**실습 및 기록 예제:**

*   Milvus 서버 실행:

    ```bash
    bash코드 복사docker run -d --name milvus \
      -p 19530:19530 \
      milvusdb/milvus:v2.2.3
    ```
*   Python SDK를 통한 Milvus 연결:

    ```python
    python코드 복사from pymilvus import connections
    connections.connect(alias="default", host="localhost", port="19530")
    ```

***

#### **2단계: 데이터 전처리 및 삽입**

**학습 목표:**

* Markdown 파일에서 데이터 추출, 전처리(HTML 및 Markdown 태그 제거).
* Milvus 컬렉션 스키마 생성 및 데이터 삽입.

**TIL 작성 항목:**

1. **Markdown 전처리**
   * HTML 및 Markdown 태그 제거 방법.
   * BeautifulSoup과 정규식을 활용한 전처리.
2. **Milvus 컬렉션 생성**
   * 컬렉션 스키마 정의 및 생성 코드.
3. **임베딩 생성 및 데이터 삽입**
   * 텍스트를 청크로 분리하여 임베딩 생성 및 Milvus 삽입.

**실습 및 기록 예제:**

*   Markdown 파일 전처리 코드:

    ```python
    python코드 복사def remove_html_tags(text):
        soup = BeautifulSoup(text, "html.parser")
        return soup.get_text()
    ```
*   Milvus 컬렉션 스키마 생성:

    ```python
    python코드 복사from pymilvus import FieldSchema, CollectionSchema, DataType, Collection

    fields = [
        FieldSchema(name="id", dtype=DataType.INT64, is_primary=True, auto_id=True),
        FieldSchema(name="embedding", dtype=DataType.FLOAT_VECTOR, dim=1024),
        FieldSchema(name="content", dtype=DataType.VARCHAR, max_length=3000),
        FieldSchema(name="tag", dtype=DataType.VARCHAR, max_length=255),
    ]
    schema = CollectionSchema(fields)
    collection = Collection("example_collection", schema)
    ```

***

#### **3단계: 효율적인 검색 로직 구현**

**학습 목표:**

* Milvus에서 태그 기반 검색 및 벡터 검색 구현.
* 태그를 활용한 검색 범위 제한 및 정확도 향상.

**TIL 작성 항목:**

1. **태그 기반 검색**
   * Milvus의 `query()` 메서드를 활용하여 태그로 필터링.
2. **벡터 유사도 검색**
   * Milvus의 `search()` 메서드를 활용하여 벡터 검색.
3. **태그와 벡터 검색의 조합**
   * 태그로 후보를 좁힌 뒤 벡터 검색으로 최적화.

**실습 및 기록 예제:**

*   태그 검색:

    ```python
    python코드 복사tag_results = collection.query(
        expr="tag == 'example_tag'",
        output_fields=["content", "tag"]
    )
    ```
*   벡터 검색:

    ```python
    python코드 복사search_results = collection.search(
        data=[query_embedding],
        anns_field="embedding",
        param={"metric_type": "L2", "params": {"nprobe": 10}},
        limit=5,
        output_fields=["content", "tag"]
    )
    ```

***

#### **4단계: 추가 최적화 및 기능 확장**

**학습 목표:**

* 검색 속도와 정확도를 개선하기 위한 최적화 기법.
* BERT를 활용한 태그와 입력 쿼리 간 유사도 계산.
* 멀티-모달 검색(ElasticSearch와의 통합).

**TIL 작성 항목:**

1. **검색 성능 최적화**
   * `nprobe` 값 조정으로 Milvus 검색 성능 개선.
2. **BERT 기반 태그 유사도 계산**
   * 입력 쿼리와 태그의 문장 임베딩 비교.
3. **ElasticSearch와 Milvus 통합**
   * 텍스트 검색을 ElasticSearch로, 벡터 검색을 Milvus로 처리.

***

#### **5단계: Markdown 기반 TIL 정리 템플릿**

**TIL 구조 제안:**

````markdown
markdown코드 복사# TIL: Milvus 기반 벡터 검색 시스템 구축

## 📌 오늘 배운 것
- Milvus 기본 개념 및 설치 방법.
- Markdown 데이터 전처리 방법.
- 태그와 벡터를 활용한 효율적인 검색 구현.

---

## 📂 학습 내용

### 1️⃣ Milvus 설치 및 환경 설정
- Docker 명령어를 사용하여 Milvus 서버 실행.
- Python SDK로 Milvus에 연결.

### 2️⃣ Markdown 데이터 전처리
- HTML 및 Markdown 태그 제거 코드:
  ```python
  def remove_html_tags(text):
      ...
````

#### 3️⃣ 검색 로직 구현

*   태그 검색:

    ```python
    python코드 복사results = collection.query(expr="tag == 'example'", output_fields=["content"])
    ```
*   벡터 검색:

    ```python
    python코드 복사...
    ```
