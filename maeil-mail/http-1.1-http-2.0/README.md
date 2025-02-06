# HTTP/1.1 과 HTTP/2.0

### HTTP/1.1 특징

:small\_blue\_diamond:**1997년도 발표(RFC 2068, 이후 RFC 2616으로 개선됨)**

:small\_blue\_diamond:현재까지 가장 널리 사용되는 HTTP 버전

:small\_blue\_diamond:기본적으로 **텍스트 기반 프로토콜**

#### 주요 특징 :&#x20;

* **지속적 연결(Persistent Connection)**
  * 하나의 TCP 연결을 여러 요청/응답에 사용(Keep-Alive)
* **파이프라이닝(Pipelining, but 제한적)**
  * 여러 요청을 연속적으로 보낼 수 있지만, 응답 순서가 중요하여 널리 사용되지 않음.
* **헤더 크기가 큼** (매 요청마다 중복된 헤더 전송)
* **병목 현상 발생(Head-of-Line Blocking)**
  * 하나의 요청이 지연되면 이후 요청들도 영향을 받음.

#### 장점

* 비교적 단순한 구조
* 대부분의 웹서버, 브라우저가 지원
* Keep-Alive 덕분에 HTTP/1.0 보다 성능 개선

#### 단점

* **다중 요청 처리 어려움**(병목 현상)
* **헤더 중복 문제** (매번 큰 HTTP 헤더가 포함됨)
* **TCP 연결 효율성 낮음**(여러 개의 요청을 위해 새 연결을 자주 생성)

***

### HTTP/2.0 특징

:small\_blue\_diamond:**2015년 발표(RFC 7540)**

:small\_blue\_diamond:HTTP/1.1 의 성능 문제를 해결하기 위해 개발

#### 주요 특징:

* **바이너리 프로토콜(Binary Protocol)**
  * HTTP/1.1 의 텍스트 기반 프로토콜 대신, [<mark style="background-color:blue;">**이진(Binary)**</mark> <mark style="background-color:blue;"></mark><mark style="background-color:blue;">데이터 형식</mark>](undefined.md) 사용
* <mark style="background-color:blue;">**멀티플렉싱(Multiplexing)**</mark>
  * 하나의 TCP 연결에서 **여러 개의 요청/응답을 동시에 처리 가능**
* **헤더 압축(**<mark style="background-color:blue;">**HPACK 압축**</mark>**)**
  * 중복되는 HTTP 헤더를 효율적으로 압축하여 전송량 절감.
* **서버 푸시(Server Push)**
  * 클라이언트가 요청하지 않은 리소스도 서버가 미리 전송 가능
* **우선순위 지정(Stream Prioritization)**
  * 요청마다 우선순위를 설정하여 중요한 리소스를 먼저 전송 가능

#### 장점

* **속도 개선(Multiplexing 덕분에 요청/응답 지연 감소)**
* 헤더 압축으로 데이터 절약
* 서버 푸시를 통한 빠른 로딩 가능

#### 단점

* TCP 연결 하나가 끊기면 모든 요청이 영향을 받음(<mark style="background-color:blue;">QUIC 프로토콜</mark>이 HTTP/3 에서 이를 해결)
* 구현이 복잡(기존 HTTP/1.1 보다 구조가 복잡함)
* 브라우저 & 서버가 HTTP/2.0 을 지원해야함.

***

### HTTP/1.1 vs HTTP/2.0 비교 표

| **전송 방식** | 텍스트 기반 | 바이너리 기반 |
| --------- | ------ | ------- |

| **다중 요청 처리** | 직렬 요청 (순차적) | 멀티플렉싱 (동시 요청) |
| ------------ | ----------- | ------------- |

| **헤더 크기** | 요청마다 전체 헤더 전송 | HPACK으로 헤더 압축 |
| --------- | ------------- | ------------- |

| **성능** | 요청당 하나씩 처리 (병목 가능) | 동시 다중 요청 가능 |
| ------ | ------------------ | ----------- |

| **서버 푸시** | ❌ 지원 안 함 | ✅ 가능 |
| --------- | -------- | ---- |

| **우선순위 지정** | ❌ 없음 | ✅ 가능 |
| ----------- | ---- | ---- |

| **지원 브라우저** | 모든 브라우저 | 최신 브라우저 지원 필요 |
| ----------- | ------- | ------------- |

***

### HTTP/2.0 은 어디에서 많이 사용될까?

* **Google, YouTube, Facebook, Twitter 등 대형 웹사이트**
* **CDN(Content Delivery Network) 서비스**
* **SPA(Single Page Application) 기반 웹사이트**
* **REST API 에서 성능 개선을 위해 사용**



하지만 HHTP/2.0 도 TCP 기반이라 지연 시간이 발생할 수 있음. 이를 해결하기 위해 **HTTP/3(UDP 기반 QUIC 프로토콜)** 이 등장
