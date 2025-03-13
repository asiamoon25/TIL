# export 테스트

<figure><img src="../../../../.gitbook/assets/image (33).png" alt=""><figcaption></figcaption></figure>

**읽기 속도(Read Speed)** 는 비슷한 수준이지만 **쓰기 속도(Write Speed)** 가 회사 노트북에서 약 60% 정도 느림.

***

### 서버 스펙

120GB 정도 되는 용량을 import 하기 위해서 target 이 되는 MySQL 서버의 스펙이 달라져야함.

**체크**

* File I/O&#x20;
* Network
* CPU (threads 를 위해)

이 두 개가 중요해짐.
