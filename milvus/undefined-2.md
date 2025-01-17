# 추가 최적화 및 기능 확장

## 최적화의 필요성

Milvus 를 효율적으로 사용하는 경우에도 대규모 데이터셋이나 복잡한 검색 요구사항이 있으면 성능 문제가 발생할 수 있음. 추가 최적화와 기능 확장을 통해 검색 속도와 정확성을 더욱 높이고, 다양한 활용 사례에 맞게 기능을 확장할 수 있음.

***

## 추가 최적화



### 1. 인덱스의 최적화

Milvus 의 인덱스는 검색 속도와 정확성에 직접적인 영향을 미침. 데이터 특성에 따라 적합한 인덱스를 선택하고 매개변수를 조정해야함.

#### 주요 인덱스 유형 :&#x20;

1. IVF\_FLAT:
   1. 대규모 데이터셋에서 근사 검색을 위해 클러스터링 기반.
   2. `nlist` 값이 클 수록 정확도가 높아지지만 메모리와 계산량이 증가&#x20;

```python
index_params = {"index_type": "IVF_FLAT", "metric_type": "COSINE", "params": {"nlist": 128}}
collection.create_index(field_name="embedding", index_params=index_params)
```

2. HNSW :&#x20;
   1. 그래프 기반 인덱스로 고성능 검색 제공
   2. 설정 매개변수:
      1. `M` : 각 노드의 연결성(값이 클수록 정확도 증가)
      2. `efConstruction`: 인덱스 생성 시 탐색 깊이

```python
index_params = {"index_type": "HNSW", "metric_type": "COSINE", "params": {"M": 16, "efConstruction": 200}}
collection.create_index(field_name="embedding", index_params=index_params)
```

3. IVF\_PQ:
   1. IVF\_FLAT 보다 더 낮은 메모리를 사용하며, 적정 속도와 정확도를 제공

### 2. 검색 매개변수 튜닝

검색 시 사용되는 매개변수 `nprobe` 는 검색 정확도와 속도에 영향을 미침.

* nprobe"
  * 검색할 클러스터의 개수
  * 값이 클 수록 정확도가 증가하지만, 검색 속도가 느려짐.
  * 예:

```python
search_params = {"metric_type": "COSINE", "params": {"nprobe": 10}}
```

* HNSW-sepcific:
  * `ef` 값으로 검색 깊이를 조정(값이 클 수록 정확도 증가):

```python
search_params = {"metric_type": "COSINE", "params": {"ef": 64}}
```



### 3. 하드웨어 최적화

#### 1. 메모리 관리

* Milvus 는 검색 속도를 위해 메모리를 적극적으로 사용하므로 적절한 메모리 크기를 확보.
* 사용 가능한 메모리 이상으로 데이터가 클 경우 디스크 기반 검색(SQ8 인덱스)을 고려

#### 2. 멀티 스레드 및 병렬 처리

* Milvus 는 멀티 스레드를 사용하므로 CPU 와 I/O 리소스를 최대한 활용

#### 3. 분산  설정

* 클러스터 모드로 Milvus 를 실행하여 성능확장:
  * DataNode 와 QueryNode 를 분리

***

## 기능 확장

### 1. 태그 기반 검색 범위 제한

검색 대상을 특정 태그로 제한하여 성능과 정확도를 향상할 수 있음.

```python
# 태그 기반 필터링
results = collection.search(
    data=[query_vector],
    anns_field="embedding",
    param={"metric_type": "COSINE", "params": {"nprobe": 10}},
    limit=10,
    expr="metadata == 'category-A'"
)

```



### 2. 텍스트 기반 검색

Milvus 는 벡터 검색 중심이지만, 텍스트 기반 검색을 위해 ElasticSearch와 결합할 수 있음.

**ElasticSearch 와 통합**

1. 텍스트 데이터는 ElasticSearch 에서 인덱싱
2. Milvus 에서 벡터 검색
3. 두 결과를 조합하여 최적의 결과 반환.



### 외부 툴과의 통합

1. Hugging Face 모델 활용
   1. Tulu3 와 같은 모델을 사용해 입력 텍스트를 벡터로 변환.

```python
from transformers import AutoTokenizer, AutoModel

tokenizer = AutoTokenizer.from_pretrained("sentence-transformers/all-MiniLM-L6-v2")
model = AutoModel.from_pretrained("sentence-transformers/all-MiniLM-L6-v2")

def get_embedding(text):
    tokens = tokenizer(text, return_tensors="pt")
    outputs = model(**tokens)
    return outputs.last_hidden_state.mean(dim=1).detach().numpy()
```

2. API 서비스로 확장
   1. FastAPI 와 Milvus 를 결합하여 검색 API 를 구축

```python
from fastapi import FastAPI
import pymilvus

app = FastAPI()

@app.post("/search")
def search(query: str):
    # 벡터화 및 검색 코드
    pass
```



### 평가 및 모니터링

Milvus 의 검색 성능을 주기적으로 평가하고, 문제가 발생한 경우 조정함.

**성능 평가**

1. Precision. Recall : 검색 결과의 정확도를 측정
2. Latency : 검색 속도 측정

**모니터링 도구**

* Prometheus 와 Grafana 를 사용하여 Milvus 의 성능 모니터링
