# CORS

### CORS란?

CORS(Cross-Origin Resource Sharing)는 교차 출처 리소스 공유라는 실행 중인 웹 애플리케이션이 다른 출처의 선택한 자원에 접근할 수 있는 권한을 부여하도록 브라우저에 알려주는 체제

즉, 브라우저에서 보안적인 이유로 cross-origin HTTP 요청들을 제한하는 것

**cross-origin** 요청을 하려면 서버의 동의가 필요함.\
서버가 동의한다면 브라우저에서는 요청을 허락, 동의하지 않는다면 거절

이런 허락과 거절은 http header 를 이용해서 가능하다. 이를 CORS(Cross-Origin Resource Sharing)라고 부른다.

**cross-origin**\
다음 중 같은 것이 없을 때의 경우

1.프로토콜 예) http or https

2.도메인 예) [naver.com](http://naver.com/)  != [never.com](http://never.com/)&#x20;

3.포트 예) 8080 != 3030

### 왜 필요한가?

CORS 가 없으면 다른 사이트에서 쉽게 접근이 가능하기 때문에 보안상 위험

### CORS의 동작원리

1.Simple Request의 경우 CORS Preflight Request(사전 요청) 을 발생시키지 않는다.

#### **Simple Request란?**

아래의 조건 3개가 만족되는 경우를 Simple Request라고 한다.

1.GET/HEAD/POST 사용

2.자동 설정되는 header

* Accept
* Accept-Language
* Content-Language
* Content-Type (but note the additional requirements below)
* DPR
* Downlink
* Save-Data
* Viewport-Width
* Width

3.Content Type

* application/x-www-form-urlencoded
* multipart/form-data
* text/plain

#### **Preflight request란?**

CORS 의 preflight는 CORS 의 request의 하나이며, 서버가 CORS 프로토콜을 인지하면서 준비가 된 것을, 특정 메서드와 헤더를 이용해서 체크한다.

즉, 본격적인 HTTP 요청 전에 서버 측에서 보낼 요청의 메서드와 헤더에 대해 인식하고 있는지 체크 하는 것

OPTIONS request 이고, Access-Control-Request-Method, Access-Control-Request-Headers, Origin의 세가지 HTTP 요청 헤더를 사용한다.

preflight는 일반적으로 브라우저에서 자동으로 발생된다. preflight 요청이 필요한 경우에만 나타나므로 simple request의 경우에는 생략된다.

예) Delete 요청을 하는 경우

```
OPTIONS /resource/chagari
Access-Control-Request-Method: DELETE
Access-Control-Request-Headers: origin, x-requested-with
Origin: https://chargari.com
```

이렇게 DELETE를 요청을 하기 전에 preflight를 통해 서버가 HTTPMehod("DELETE")를 허용하는지 물어본다.

서버가 허용한다면

```
HTTP/1.1 204 No Content
Connection: keep-alive
Access-Control-Allow-Origin: https://chagari.com
Access-Control-Allow-Methods: POST, GET, OPTIONS, DELETE
Access-Control-Max-Age: 86400
```

동일한 URL에 대한 요청은 Access-Control-Max-Age 헤더를 이용해 정해진 기간 내에 오는 사전요청에 대한 응답이 선택적으로 이루어질 수 있다.(캐시 기능)
