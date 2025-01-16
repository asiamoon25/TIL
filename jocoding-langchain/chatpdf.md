# ChatPDF

<figure><img src="../.gitbook/assets/image (23).png" alt=""><figcaption></figcaption></figure>

1. PDF 로드 후 스플릿
2. 스플릿 한 텍스트를 임베딩(벡터화)
3. VectorDB 에 Insert(Chroma 사용)
4. Tulu 에 질문

사용자가 질문한 것을 임베딩 -> 벡터 DB 조회 -> 조회한 데이터를 Tulu 에 질문 -> 답변

***

## Vector?

$$Vector = [1, 2]$$



### Vector DB 란?

Vector 데이터를 좀 더 효율적으로 저장하기 위한 DB

***

### Embedding?

데이터 (텍스트, 이미지, 오디오 등) -> Embedding Model -> Vector 변환

_데이터를 벡터화 하는 모델_

