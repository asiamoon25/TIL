---
description: Milvus 기본 이해 및 환경 설정
---

# Milvus 기본 이해 및 환경 설정

### Milvus 란?

Milvus는 고성능 벡터 데이터베이스(Vector Database) 로, 고차원 데이터(예 : 텍스트, 이미지, 오디오, 비디오 등)의 유사도 검색을 빠르게 수행하기 위해 설계된 데이터베이스임.



#### 특징

* 벡터 데이터 저장 : 고차원 벡터를 효율적으로 저장하고 관리
* 유사도 검색 : 벡터 간 거리 계산으로 유사한 데이터 검색(예 : 최근접 이웃 검색, k-NN)
* 확장성 : 대규모 데이터 처리를 위한 분산형 아키텍처
* 오픈소스 : Apache 2.0 라이선스로 무료 사용 가능
* 다양한 활용 사례:
  * 텍스트 검색(검색 엔진, 대화형 AI)
  * 이미지/동영상 검색
  * 추천 시스템
  * 바이오메트릭스(얼굴/지문 인식)

***

### Milvus 아키텍처

Milvus 는 여러 모듈로 구성되어 있어, 벡터 저장 및 검색 작업을 최적화함.

**주요 컴포넌트**

1. DataNode : 벡터 데이터를 저장
2. QueryNode : 유사도 검색 및 질의 처리
3. IndexNode : 벡터 데이터의 인덱싱(IVF, HNSW 등)
4. RootCoord : 전체 클러스터 조정
5. MetaStore : 메타데이터 저장(MySQL, SQLite 등).
6. Storage : 벡터 데이터를 영구적으로 저장(S3, MinIO 등)

***

### Milvus 설치 및 환경 설정

#### Docker를 사용한 Milvus 설치

Docker 및 Docker Compose 설치

```bash
# Ubuntu
sudo apt update
sudo apt install docker.io docker-compose
```

Docker Compose 작성

```yaml
version: '3.5'

services:
  etcd:
    container_name: milvus-etcd
    image: quay.io/coreos/etcd:v3.5.5
    environment:
      - ETCD_AUTO_COMPACTION_MODE=revision
      - ETCD_AUTO_COMPACTION_RETENTION=1000
      - ETCD_QUOTA_BACKEND_BYTES=4294967296
      - ETCD_SNAPSHOT_COUNT=50000
    volumes:
      - ${DOCKER_VOLUME_DIRECTORY:-.}/volumes/etcd:/etcd
    command: etcd -advertise-client-urls=http://127.0.0.1:2379 -listen-client-urls http://0.0.0.0:2379 --data-dir /etcd
    healthcheck:
      test: ["CMD", "etcdctl", "endpoint", "health"]
      interval: 30s
      timeout: 20s
      retries: 3

  minio:
    container_name: milvus-minio
    image: minio/minio:RELEASE.2023-03-20T20-16-18Z
    environment:
      MINIO_ACCESS_KEY: minioadmin
      MINIO_SECRET_KEY: minioadmin
    ports:
      - "9001:9001"
      - "9000:9000"
    volumes:
      - ${DOCKER_VOLUME_DIRECTORY:-.}/volumes/minio:/minio_data
    command: minio server /minio_data --console-address ":9001"
    healthcheck:
      test: ["CMD", "curl", "-f", "http://localhost:9000/minio/health/live"]
      interval: 30s
      timeout: 20s
      retries: 3

  standalone:
    container_name: milvus-standalone
    image: milvusdb/milvus:v2.4.15
    command: ["milvus", "run", "standalone"]
    security_opt:
      - seccomp:unconfined
    environment:
      ETCD_ENDPOINTS: etcd:2379
      MINIO_ADDRESS: minio:9000
    volumes:
      - ${DOCKER_VOLUME_DIRECTORY:-.}/volumes/milvus:/var/lib/milvus
    healthcheck:
      test: ["CMD", "curl", "-f", "http://localhost:9091/healthz"]
      interval: 30s
      start_period: 90s
      timeout: 20s
      retries: 3
    ports:
      - "19530:19530"
      - "9091:9091"
    depends_on:
      - "etcd"
      - "minio"

  attu:
    container_name: milvus-attu
    image: zilliz/attu:v2.4
    environment:
      MILVUS_URL: standalone:19530
    ports:
      - "9555:3000"
    depends_on:
      - "standalone"
    networks:
      - default

networks:
  default:
    name: milvus
```

#### Python SDK 설치 및 연결

1. Python SDK 설치

```bash
pip install pymilvus
```

2. Milvus 연결 아래 코드를 실행해 Milvus 서버와 연결을 테스트함.

```python
from pymilvus import connections

connections.connect(alias="default", host="localhost", port="19530")

print("Successfully connected to milvus!")
```



***



### Milvus Collection 생성

Milvus 는 데이터를 저장하기 위해 **Collection** 을 사용함. 각 컬렉션은 스키마(Schema) 를 정의해야함.



#### 컬렉션 스키마 구성 :&#x20;

* id : 데이터의 고유 실별자(INT64)
* embedding: 벡터 데이터(FLOAT\_VECTOR)
* metadata : 벡터와 관련된 추가 정보(예 : 태그, 텍스트 등)

**컬렉션 생성 코드**

```python
from pymilvus import FieldSchema, CollectionSchema, DataType, Collection

# 필드 정의
fields = [
    FieldSchema(name="id", dtype=DataType.INT64, is_primary=True, auto_id=True),  # ID 필드
    FieldSchema(name="embedding", dtype=DataType.FLOAT_VECTOR, dim=128),         # 벡터 필드
    FieldSchema(name="metadata", dtype=DataType.VARCHAR, max_length=500)        # 메타데이터 필드
]

# 컬렉션 스키마 정의
schema = CollectionSchema(fields, description="Example collection for vector data")

# 컬렉션 생성
collection = Collection(name="example_collection", schema=schema)

print("Collection created:", collection.name)
```

***

### 데이터 삽입

Milvus 에 데이터를 삽입하려면 벡터 데이터를 포함한 입력을 제공해야함.



**데이터 삽입 예제**

```python
import random

# 예제 데이터 생성
vectors = [[random.random() for _ in range(128)] for _ in range(10)]  # 128차원 벡터
metadata = [f"data-{i}" for i in range(10)]  # 메타데이터

# 데이터 삽입
collection.insert([vectors, metadata])
print("Data inserted successfully!")
```

***

### 데이터 검색

Milvus 는 벡터 간의 유사도 검색을 지원함.

**검색 예제**

```python
# 검색할 벡터 생성
query_vector = [random.random() for _ in range(128)]

# 유사도 검색
results = collection.search(
    data=[query_vector],
    anns_field="embedding",
    param={"metric_type": "L2", "params": {"nprobe": 10}},
    limit=5,
    output_fields=["metadata"]
)

# 검색 결과 출력
for result in results[0]:
    print(f"ID: {result.id}, Metadata: {result.entity.get('metadata')}, Distance: {result.distance}")

```

***

### Milvus 관리 명령어

**컬렉션 확인**

```python
from pymilvus import utility

collections = utility.list_collections()
print("Collections:", collections)
```

**컬렉션 삭제**

```python
# 컬렉션 삭제
collection.drop()
print("Collection deleted!")
```



***

### Milvus 의 주요 검색 알고리즘

1. IVF\_FLAT : 효율적인 근사 최근접 이웃 검색
2. HNSW : 그래프 기반의 고성능 검색 알고리즘.
3. FLAT : 완전 탐색으로 가장 정확하지만 속도가 느림.

***

### 유의점

* 메모리 관리 : Milvus 는 고성능을 위해 메모리를 많이 사용하므로, 적절한 메모리와 저장소를 준비
* 인덱스 생성 : 대량 데이터를 검색하기 전, 적절한 인덱스를 생성
* 백업 및 복원 : 중요한 데이터는 S3, MinIO 와 같은 외부 스토리지로 백업

