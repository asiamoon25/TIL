# .md 파일 정리

파일 양이 많고 내용이 정리되지 않은 상태라면, 데이터를 정리하고 파인튜닝에 활용할 수 있도록 준비하는 몇 가지 단계를 가져야함.

***

### 1. 데이터 분석 및 분류

먼저 `.md` 파일에 어떤 데이터가 포함되어 있는지 파악해야함.

* 파일 내용은 특정 주제에 집중되어 있는지(예: 기술 문서, 블로그, 회의 기록 등)
* 학습에 적합한 텍스트(예: 문장, 질문-답변 쌍) 가 얼마나 포함되어 있는지
* 불필요한 내용(코드, 주석, 헤더 등) 은 얼마나 많은지

**도구 활용**

* **Python을 사용한 간단한 키워드 분석**:
  * 자주 사용된 단어를 분석해 주요 주제를 파악

```python
from collections import Counter
import re

with open("example.md", "r", encoding="utf-8") as file:
    text = file.read()

words = re.findall(r'\w+', text.lower())
common_words = Counter(words).most_common(20)
print(common_words)
```

***

### 2. 불필요한 정보 제거

.md 파일에는 학습에 불필요한 요소가 포함되어 있을 가능성이 높음. 이를 제거하는 작업이 필요함.

**제거할 가능성이 높은 항목**:

* **Markdown 포맷** : `#`,`*` , `-` ,`[링크 텍스트](URL)` 등
* **코드 블록** : \`\`\`, `{}`, `<code>` 와 같은 코드 형식
* **HTML 태그** : `<tag>` 형태의 내용

**처리 방법**

* **정규 표현식** 을 활용해 불필요한 요소 제거:

````python
import re

with open("example.md", "r", encoding="utf-8") as file:
    text = file.read()

# Markdown 헤더 제거
text = re.sub(r'#.*', '', text)
# 링크 제거
text = re.sub(r'\[.*?\]\(.*?\)', '', text)
# 코드 블록 제거
text = re.sub(r'```.*?```', '', text, flags=re.DOTALL)
# HTML 태그 제거
text = re.sub(r'<.*?>', '', text)

with open("cleaned_data.txt", "w", encoding="utf-8") as file:
    file.write(text)

````

***

### 3. 데이터 정형화

정리된 데이터를 모델 학습에 적합한 형식(예: CSV, JSON) 으로 변환해야 함.

**예제: 질문-답변 데이터로 반환**

```python
import csv

data = [
    {"input": "What is fine-tuning?", "output": "Fine-tuning is the process of adapting a pre-trained model to a specific task."},
    {"input": "How does fine-tuning work?", "output": "Fine-tuning updates the weights of a pre-trained model using task-specific data."}
]

with open("formatted_data.csv", "w", encoding="utf-8", newline="") as file:
    writer = csv.DictWriter(file, fieldnames=["input", "output"])
    writer.writeheader()
    writer.writerows(data)
```

***

### 4. 대규모 데이터 자동화

.md 파일의 양이 많다면 수동으로 정리하기 어려울 수 있으니, 자동화된 도구를 활용

**도구 추천**:

1. **Python 스크립트**:
   1. 위의 예제처럼 반복적으로 전처리 작업을 수행하도록 자동화.
2. **OpenAI Whisper(문자 변환)**:
   1. 문서가 음성 데이터로 변환된 경우 텍스트로 변환 가능
3. **RegEx101:**
   1. 정규 표현식을 테스트하고 데이터 패턴을 정리
4. **LangChain**:
   1. .md 파일 내 특정 정보를 추출하고 요약할 때 유용함.

***

### 5. 데이터 검증

전처리된 데이터가 학습에 적합한지 확인해야함.

* **샘플 데이터 확인** : 학습할 데이터가 모델이 이해할 수 있는 구조인지 샘플링
* **라벨 유효성 검증** : 질문-답변 쌍이나 분류 데이터가 일관성 있는지 확인.

***

### 6. 클라우드 리소스 활용

정리가 끝난 데이터를 클라우드에서 처리하면 속도를 높일 수 있음.

* **Google Colab :** GPU/TPU 무료 사용
* **AWS/GCP** : 대규모 데이터 처리에 적합.

