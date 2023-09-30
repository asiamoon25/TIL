# JWT

## JWT 란?

### JSON WEB TOKEN

JWT는 정보를 JSON으로 안전하게 전송하기 위한 토큰. JWT는 HMAC알고리즘 과 RSA, ECDSA(타원곡선 디지털 서명 알고리즘) 을 사용한다.

### 언제 사용?

**권한 부여** : 사용자가 홈페이지에 로그인 했을 때 JWT를 반환하며 해당 유저에 대한 권한을 반환한다.

**정보 교환** : public/private key 쌍을 사용하여 JWT에 서명할 수 있기 때문에 발신자가 누구인지 확인할 수 있고 header 와 payload를 사용하여 서명을 하므로 콘텐츠가 변조되지 않았는지 확인 할 수 있다.

### JWT 구조

JWT는 점(.)으로 구분된 세 부분으로 구성된다.

* 1.header
* 2.payload
* 3.signature

따라서 구조는 다음과 같다.

xxxx.yyyy.zzzz

**1.Header**

헤더는 JWT의 유형과 사용한 알고리즘을 나타낸다. 이것은 Base64Url로 인코딩 되어 나타난다.

```
{
    "alg" : "HS256",
    "typ" : "JWT"
}
```

**2.PayLoad**

JWT의 두번째 부분이며 클레임을 포함하는 payload 이다.

클레임은 세 가지 유형이 있다.

**등록된 claim** : iss(발급자), exp(만료시간), sub(제목), aud(대상) 및 기타

**공개 claim** : 사용자 마음대로 정의할 수 있다. 하지만 충돌을 방지하기 위해서 IANA JSON Web Token Registry에서 정의하거나 URI로 정의해야한다.

**비공개 claim** : 사용에 동의하고 등록된 claim이나 공개 claim이 아닌 당사자 간에 정보를 공유하기 위해 생성된 맞춤 claim

payload 예시

```
{
    "sub" : "ㅁㄴㅇㄻ",
    "name" : "chagari",
    "admin" : true
}
```

이 이후에 Base64Url로 인코딩이 되어 JWT의 두번째 부분을 형성한다.

**3.Signature**

서명(signature)을 생성하려면 인코딩된 헤더, 페이로드, secretkey, 헤더에 지정된 알고리즘을 가져와서 서명을 해야한다.

예로 HMAC SHA256 알고리즘을 사용하려는 경우 Signature은 다음과 같은 방식으로 생성된다.

```
HMACSHA256(
    base64UrlEncode(header) + "." +
    base64UrlEncode(payload),
    secretkey)
```

이렇게 서명을 생성하면 메시지가 도중에 변경되지 않았는지 확인하는데 사용된다.

이 세 가지가 JWT를 이루고 있다.

### 작동

사용자가 Login 성공시 JWT를 반환 -> 사용자 측에서 저장 -> 사용자가 권한이 필요한 경로로 접근하려 할 때 Bearer 스키마를 사용해 Authorization 헤더에 JWT를 담아서 보내야 한다.

```
Authorization: Bearer <token>
```

**주의점** :\
1.HTTP 헤더를 통해 JWT를 보내는 경우 토큰이 너무 커지지 않게 해야함. 일부 서버는 8KB 이상의 헤더를 허용하지 않음.\
많은 정보를 넣을경우 Auth0 Fine-Grained Authorization과 같은 대체 솔루션이 필요.

2.보안 취약하기 때문에 민감한 세션 데이터를 브라우저 저장소에 저장하면 안된다.

3.필요 이상으로 오래 보관해서는 안된다.

### 장점

1.사용자 인증에 필요한 모든 정보는 토큰 자체에 포함하기 때문에 별도의 인증 저장소가 필요 없다.

2.트래픽에 대한 부담이 낮다.

3.REST 서비스로 제공이 가능하다.

### 단점

1.토큰을 클라이언트에 저장되면 수정이 불가 하다.

2.필드가 추가되면 토큰이 커진다.
