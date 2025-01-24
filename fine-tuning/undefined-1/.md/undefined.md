# 파일이 많고 패턴을 찾기 어려움

**자동화된 분석 도구와 스크립트** 를 사용해 패턴을 추출하고 데이터를 이해해야함.

***

### 1. 간단한 샘플링

모든 파일을 한 번에 분석하기 보다는, **일부 파일** 을 무작위로 샘플링하여 내용을 살펴보는 것이 좋음.



**샘플링 코드**

```python
import os
import random

directory = "/path/to/md_files"
sample_files = random.sample(os.listdir(directory),5) # 무작위 5개 파일 선택

# 샘플 출력
for file_name in sample_files:
    with open(os.path.join(directory, file_name), "r", encoding="utf-8") as file:
        print(f"=== {file_name} ===")
        print(file.read(500)) # 첫 500자 출력
        print("\n")
```

***

### 2. 공통 패턴 찾기

샘플링한 데이터를 통해 자주 반복되는 형식을 파악함.  **자주 등장하는 요소** 를 자동으로 탐지하는 방법을 사용함.

**예: 자주 등장하는 단어/구조 탐지**

```python
from collections import Counter
import re

# 모든 파일에서 단어 빈도수 찾기
word_counter = Counter()

for file_name in os.listdir(directory):
    with open(os.path.join(directory, file_name), "r", encoding="utf-8") as file:
        text = file.read()
        words = re.findall(r'\w+', text.lower()) # 단어만 추출
        word_counter.update(words)
        
# 가장 많이 등장한 20개 단어 출력
print(word_counter.most_common(20))
```

***

### 3.  구조적인 패턴 추출

Markdown 파일에서 **특정 태그** 나 **구조적인 패턴** 을 분석하려면 정규 표현식을 사용함.



**Markdown** 헤더와 코드 블록 패턴 추출:

````python
import re

# Markdown 헤더와 코드 블록 추출
pattern_header = r"^(#{1,6}\s*(.*)" # 헤더 패턴
pattern_code_block = r"```(.*?)```"

for file_name in os.listdir(directory):
    with open(os.path.join(directory, file_name), "r", encoding="utf-8") as file:
        text = file.read()
        headers = re.findall(patter_header, text, flags=re.MULTILINE)
        code_blocks = re.findall(pattern_code_block, text, flags=re.DOTALL)
        
        print(f"Headers in {file_name}: {headers}")
        print(f"Code blocks in {file_name}: {code_blocks}")
````

***

### 4. 텍스트 클러스터링을 통한 패턴 발견

데이터 수가 너무 많아 수동 분석이 어렵다면 **클러스터링 알고리즘** 으로 비슷한 내용을 자동으로 그룹화할 수 있음.



**사용 방법**:

* **TF-IDF** : 파일의 텍스트 숫자를 벡터로 변환.
* **K-Means 클러스터링** : 비슷한 데이터를 자동으로 그룹화

**예제 코드(TF-IDF + K-Means)**:

```python
from sklearn.features_extraction.text import TfdfVectorizer
from sklearn.cluster import KMeans
import os

# 모든 파일 내용 읽기
texts = []
file_names = []

for file_name in os.listdir(directory):
    with open(os.path.join(directory, file_name), "r", encoding="utf-8") as file:
    file_names.append(file_name)
    
# TF-IDF 변환
vectorizer = TfidVectorizer(max_features=1000, stop_words="english")
X = vectorizer.fit_transform(texts)

# K-Means 클러스터링
num_clusters = 5
kmeans = KMeans(n_clusters=num_clusters, random_state=42)
kmeans.fit(X)

# 각 파일이 속한 클러스터 출력
```

***

### 5. 파일 요약 및 키워드 추출

많은 파일을 빠르게 분석하려면 **텍스트 요약** 또는 **키워드 추출** 기법을 사용하여 주요 내용을 파악할 수 있음.



**Python 요약 예제(Sumy 라이브러리)**:

```python
from sumy.parsers.plaintext import PlaintextParser
from sumy.nlp.tokenizers import Tokenizer
from sumy.summarizers.lsa import LsaSummarizer

# 텍스트 요약
for file_name in os.listdir(directory):
    with open(os.path.join(directory, file_name), "r", encoding="utf-8") as file:
        text = file.read()
        parser = PlaintextParser.from_string(text, Tokenizer("english"))
        summarizer = LsaSummarizer()
        summary = summarizer(parser.document, 3)  # 요약 문장 3개

        print(f"=== Summary for {file_name} ===")
        for sentence in summary:
            print(sentence)
```

***

### 6. 클라우드 서비스 활용

파일이 너무 많아 로컬에서 처리하기 어렵다면 클라우드 서비스를 활용해 작업할 수 있음.

* **Google Colab** : 무료 GPU 및 대규모 데이터 처리 가능
* **Hugging Face Pipelines** : 텍스트 요약, 키워드 추출 등



