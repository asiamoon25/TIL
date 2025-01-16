# 효율적인 검색 로직 구현

#### **1. Milvus 검색의 기본 개념**

* Milvus는 **유사도 검색**을 통해 입력 벡터와 데이터베이스에 저장된 벡터 간의 거리를 계산하여 가장 가까운 벡터를 반환
* 검색은 주로 다음과 같은 두 가지 설정에 의해 좌우됨:
  1. **인덱스 설정**: 데이터에 대한 검색 구조를 정의.
  2. **매개변수 튜닝**: 검색 성능(속도 vs 정확도)을 최적화.

***

#### **2. 효율적인 검색 로직 구현 과정**

**Step 1. 컬렉션 생성**

먼저 검색에 사용할 컬렉션을 생성

```python
from pymilvus import Collection, FieldSchema, CollectionSchema, DataType

# 필드 정의
fields = [
    FieldSchema(name="id", dtype=DataType.INT64, is_primary=True, auto_id=True),
    FieldSchema(name="embedding", dtype=DataType.FLOAT_VECTOR, dim=128),  # 벡터 필드
    FieldSchema(name="metadata", dtype=DataType.VARCHAR, max_length=500)  # 메타데이터 필드
]

# 컬렉션 스키마 정의
schema = CollectionSchema(fields, description="Example Collection")

# 컬렉션 생성
collection = Collection(name="example_collection", schema=schema)
```

***

**Step 2. 데이터 삽입**

검색 대상이 되는 벡터 데이터를 삽입

```python
import random

# 데이터 생성
vectors = [[random.random() for _ in range(128)] for _ in range(1000)]  # 128차원 벡터
metadata = [f"data-{i}" for i in range(1000)]

# 데이터 삽입
collection.insert([vectors, metadata])
print("Total records:", collection.num_entities)
```

***

**Step 3. 인덱스 설정**

Milvus는 검색 성능을 최적화하기 위해 다양한 인덱스 유형을 제공한다. 가장 일반적인 인덱스 유형은 다음과 같음.:

* **IVF\_FLAT**: 근사 최근접 이웃 검색에 적합, 메모리 소모 적음.
* **HNSW**: 그래프 기반 검색, 고성능 검색에 적합.
* **FLAT**: 완전 탐색으로 정확하지만 속도가 느림.

**예제: IVF\_FLAT 인덱스 생성**

```python
# IVF_FLAT 인덱스 생성
index_params = {"index_type": "IVF_FLAT", "metric_type": "COSINE", "params": {"nlist": 128}}
collection.create_index(field_name="embedding", index_params=index_params)
print("Index created successfully!")
```

**참고: 주요 매개변수**

* **index\_type**: 사용할 인덱스 알고리즘 (예: IVF\_FLAT, HNSW).
* **metric\_type**: 거리 측정 방식 (예: COSINE, L2, IP).
* **nlist**: 검색 공간을 나누는 클러스터의 수 (값이 클수록 정확도 증가, 속도 감소).

***

**Step 4. 검색 매개변수 최적화**

검색 시 성능은 `search()` 호출 시 사용되는 매개변수에 따라 달라짐.

**기본 검색 코드:**

```python
query_vector = [random.random() for _ in range(128)]  # 검색할 벡터

# 검색 수행
results = collection.search(
    data=[query_vector],                # 검색할 벡터
    anns_field="embedding",             # 검색할 필드
    param={"metric_type": "COSINE", "params": {"nprobe": 10}},  # 검색 매개변수
    limit=5,                            # 반환할 결과 수
    output_fields=["metadata"]          # 반환할 추가 필드
)

# 검색 결과 출력
for result in results[0]:
    print(f"ID: {result.id}, Metadata: {result.entity.get('metadata')}, Score: {result.score}")
```

**매개변수 설명**

1. **anns\_field**: 검색할 벡터 필드 이름.
2. **param**:
   * **metric\_type**: 거리 계산 방식 (예: COSINE, L2).
   * **nprobe**: 검색 시 확인할 클러스터 수 (값이 클수록 정확도 증가, 속도 감소).
3. **limit**: 반환할 검색 결과의 최대 개수.

***

**Step 5. 결과 후처리**

검색 결과에서 추가적인 정렬이나 필터링을 수행하여 최종 결과를 최적화함.

```python
# 결과 정렬
sorted_results = sorted(results[0], key=lambda x: x.score, reverse=True)

# 출력
for result in sorted_results:
    print(f"ID: {result.id}, Metadata: {result.entity.get('metadata')}, Score: {result.score}")
```

***

#### **3. 최적화 전략**

1. **인덱스 튜닝**:
   * **nlist** 값을 조정:
     * 값이 클수록 정확도가 증가하지만, 메모리 사용량과 검색 시간이 증가.
   * HNSW를 사용할 경우, `efConstruction`과 `M` 값을 조정.
2. **검색 매개변수 최적화**:
   * **nprobe** 값을 실험적으로 조정하여 검색 정확도와 속도 간 균형을 찾음.
     * 예: `nprobe=10` → 기본, `nprobe=64` → 높은 정확도.
3. **쿼리 필터링**:
   * 태그 또는 메타데이터를 기반으로 검색 범위를 좁히면 속도를 향상할 수 있음.
   *   예:

       ```python
       results = collection.search(
           data=[query_vector],
           anns_field="embedding",
           param={"metric_type": "COSINE", "params": {"nprobe": 10}},
           limit=5,
           expr="metadata LIKE 'data-%'"
       )
       ```
4. **병렬 처리**:
   * Milvus는 여러 클러스터에서 병렬 검색을 수행할 수 있음.
   * 고성능 검색이 필요하면 클러스터 설정을 조정.

***

