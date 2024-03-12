# EDA(Event Driven Architecture)

## EDA(Event Driven Architecture)

* Event Driven Architecture

#### Event Driven 이란?

분산된 시스템에서 **이벤트**를 생성(발행)하고 발행된 이벤트를 수신자에게 전송하는 구조로 수신자는 그 이벤트를 처리하는 방식의 `아키텍처`

**MSA** 같은 분산 아키텍쳐 환경에서 상호 간 결합도를 낮추기 위해 비동기 방식으로 메시지를 전달하는 패턴. 느슨한 결합을 지원하고 유연성, 탄력성, 확장성 있는 시스템을 구현할 수 있다.

주로 **Message Broker(kafa, RabbitMQ)** 와 결합하여 구성된다.

#### 장점

1.  **독립적인 확장 및 장애**

    서비스를 분리하면서 서로를 인식하지 못하게 하고 이벤트 라우터만 인식하기 때문에 어디서 온 녀석인지 모름. 그 말은 즉, 다른 하나가 망가져도 다른 하나는 정상적인 작동을 할 수 있음.
2.  **빠른 개발**

    이벤트 라우터가 이벤트를 자동으로 필터링하고 소비자에게 전달함. Producer 와 Consumer 프로세스 간에 과도한 조정 없애 개발과정을 가속 시킨다.
3.  **비용절감**

    이벤트 기반 아키텍처는 푸시 기반이므로, 모든 작업이 이벤트가 라우터에 인식될 때 온디맨드 방식으로 이루어진다. 이렇게 하면 이벤트를 확인하기 위해 지속적 폴링\*\*(계속 지켜보는 것)\*\* 을

    위해 지속적인 폴링 비용을 지불할 필요가 없음. 즉, 네트워크 대역폭 사용량, CPU 활용율, 유휴 장치 용량 및 SSL/TLS 핸드셰이크가 줄어든다.
4.  **쉬워진 감사(Thanks 아님)**

    이벤트 라우터는 애플리케이션을 감사하고 정책을 정의하는 중장 집중식 로케이션 역할을 한다.

    이러한 정책은 라우터를 게시 및 구독할 수 있는 사용자를 제한하고 데이터에 엑세스할 권한이 부여되는 사용자와 리소스를 제어할 수 있음. 또한 전송 중인 이벤트와 저장된 이벤트를 모두 암호화 할 수 있음.

#### **단점**

1. Broker Dependency
   * 시스템 간 의존도 낮아지고 메시지 브로커에 대한 의존성 발생한다.
2. Transction 단위 분리
   * 장애나 이슈 발생 시 Retry / Rollback 에 대한 고려가 필요하다.
3. 시스템 Flow 파악 어려움
4. 디버깅 어려움

<figure><img src="../.gitbook/assets/image (3) (1).png" alt=""><figcaption></figcaption></figure>

#### **Event Driven Architecture 필수 구성 요소**

**1. Even Generator (Publisher, Producer, Creater)**

* 표준화된 형식의 이벤트를 생성하고 생성된 이벤트는 Event channel 로 전송한다.

**2. Event Channel (Bus)**

* 이벤트를 필요로 하는 시스템까지 발송하는 역할이다.

**3. Event Processing Engine (Consumer, Processor)**

\-  수신한 이벤트를 식별/처리 하는 역할을 한다. 처리 결과에 따라 새로운 이벤트를 생성할 수 있는데 Consumer 는 이벤트의 송신자에 대한 정보를 알 필요가 없다.





<figure><img src="../.gitbook/assets/image (4).png" alt=""><figcaption></figcaption></figure>

#### **Event Bus (Store)**

Queue 처리 방식으로는 대표적으로 ActiveMQ, RabbitMQ 가 있으며 메시지 모델로는 Apache Kafka를 많이 활용한다. 이벤트를 들고 나름

<figure><img src="../.gitbook/assets/image (5).png" alt=""><figcaption></figcaption></figure>

#### **Event Driven Architecture 선택 시 고려사항**

마이크로 서비스 또는 분리된 구성요소를 사용하는 애플리케이션에는 적합하지만

이벤트 기반 아키텍처를 사용하기 위해선 다음과 같은 고려사항이 필요하다.

1. 지속 가능하고 안정적인 이벤트 소스여야 한다.
2. 시스템 성능이 전역 범위 또는 비탄력적 데이터베이스에 의존하지 않아야 된다.
3. 코드 분석을 사용한 정적 추적은 지원하지 않는다.
4. 데이터의 중복 삭제 및 순서 지정 방법을 고려해야 한다.
