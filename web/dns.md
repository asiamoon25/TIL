# DNS

## DNS란?

Domain Name System의 약자, 호스트의 도메인 이름을 호스트의 네트워크 주소로 바꾸거나 그 반대의 변환을 수행할수 있도록 개발이 되었다.

인터넷 도메인 주소로 체계로써 TCP/IP의 응용에서, www.happytuk.com으로 연결했을 때 도메인 이름을 127.0.0.1과 같은 ip주소로 변환하고 라우팅 정보를 제공하는 분산형 데이터베이스시스템이다.

옛날 전화 교환원을 생각하면 이해하기 쉬울 거 같다.

옛날 전화 교환원은 내가 전화를 걸었을 때 전화를 건 상대한테 전화를 연결 시켜주는 중간 다리 느낌의 일을 했다.

그것과 비슷하다고 보면 된다.

실제 통신사에서 내가 상대방에게 전화를 걸었을 때 한번 거치고 누구에가 가는지 파악한다음에 가는걸로 알고 있다.

통화기록 같은게 남는걸 보면 그렇다고 생각이 된다.

### 그러면 이것 도메인 이름들은 어디에 저장될까?

DNS 서버에 저장....

### 어떻게 이런 일이 가능할까?

인터넷에는 공간이 존재하는데 이름 공간과 주소 공간이 존재한다.

그러면 이름 과 주소가 의미하는 것은 무엇일까?

**이름** : 보통, 인간이 식별할 수 있는 명칭 ( google? )

**주소** : 보통, 기계가 식별할 수 있는 숫자 주소 ( IP 주소)

**이름 공간** : 도메인 이름을 저장하는 공간적 구성형태, 계층적 트리구조를 취함. ( 0 레벨 루트부터 최대 128개 레벨 가능)

![sadfsadf.png](https://dev.azure.com/jeffkang/c7fa7cf4-3646-4a12-922d-4a43cae15861/\_apis/git/repositories/f57dcf48-f623-4e6c-802a-06f899f38955/Items?path=/.attachments/sadfsadf-0b31198b-54e2-40e7-9039-7ac810d45dcd.png\&download=false\&resolveLfs=true&%24format=octetStream\&api-version=5.0-preview.1\&sanitize=true\&versionDescriptor.version=wikiMaster)

위의 이미지를 참고하면 될 거 같다.

각 도메인은 그에 해당하는 도메인 이름을 가지며, 상위 도메인은 하위 도메인에 대한 정보를 유지 관리하는 형태를 취한다.

**주소 공간** : 인터넷 상의 논리적 IP주소가 부여될 수 있는 주소 공간, 공인 주소는 보통 지리적으로 큰 블록 단위로 주소

할당을 한다.

**IP주소** : 네트워크 계층 상에서 논리적으로 지정되는 식별 주소

이제 이렇게 각각의 공간에 할당 된 이름과 주소들을 매핑을 하여 이름으로 접근했을 때 주소로 변환하는 일을 해줘야 하는데 여기서 나오는 녀석은 Resolver(해석기)라는 녀석이다.

## Resolver(해석기)

### 개념

DNS 서버에 대한 액세스를 수행하는 어플리케이션이다.

네임서버(NS)에 원하는 호스트에 대한 정보를 조회하고, 그 응답으로 부터 필요 정보를 추출하는 DNS클라이언트용 프로그램IP주소와 이름 간을 서로 매핑하기 위해 적어도 1 이상의 네임서버에 접근하여 그 네임서버에 있는 정보를 사용한다.

### 특징

유닉스에서 Resolver에 대한 구성 설정 파일

/etc/ resolv.conf 파일에 디폴트 도메인과 네임서버 명칭 및 IP주소가 있다.

작고 간결한 라이브러리 루틴

http, ftp, telnet 등에 연결되는 작고 간결한 라이브러리 루틴

이러한 것들로 인해서 이름으로 ip주소를 찾는 일이 가능해진다

### 흐름

\
간단하게 보면 위의 이미지 처럼 흐름이 흐른다.

<figure><img src="https://dev.azure.com/jeffkang/c7fa7cf4-3646-4a12-922d-4a43cae15861/_apis/git/repositories/f57dcf48-f623-4e6c-802a-06f899f38955/Items?path=/.attachments/ggf-f34ec742-a43b-4169-b614-7d5ab7b51502.png&#x26;download=false&#x26;resolveLfs=true&#x26;%24format=octetStream&#x26;api-version=5.0-preview.1&#x26;sanitize=true&#x26;versionDescriptor.version=wikiMaster" alt=""><figcaption></figcaption></figure>

먼저 위의 그림처럼 순서는 이렇다.

이름 공간이 계층적 트리 구조인데 Root가 가장 최상위 레벨이다. 그래서 Root먼저 가서 물어보는 순서이다.

1. [example.com](http://example.com/)  어디 있어? 라고 리졸버에게 보낸다.
2. 리졸버는 이 요청을 Root DNS Server에 보낸다. Root DNS Server에 없으면 Root DNS Server는 "잘 모름;; 하지만 .com TLD DNS Server에 물어보면 될듯 ㅇㅇ"하면서 주소 반환 후에 다시 리졸버로 보냄.
3. 리졸버는 이번에 TLD DNS Server로 요청을 보냄. "님 [example.com](http://example.com/)  주소 뭔지 앎?"
4. TLD DNS 서버는 "ㄴㄴ 나도 잘 모름 여기로 가보셈" 이런 식으로 찾을 때까지 이짓을 반복한다.
5. 결국 찾으면 그 DNS 서버에서 리졸버로 리턴해주고 리졸버는 사용자에게 ip 주소?를 보낸뒤 사용자는 ip주소로 해당 서버로 접속한다.
