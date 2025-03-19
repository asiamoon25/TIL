# Java 24

2025년 3월 18일 발표된 JDK 24



### 1. 주요 새로운 기능(Major New Functionality)

#### 1.1 언어 기능 개선(Language Previews)

* **Primitive Types in Patterns, instanceof, and switch(JEP 488, Second Preview)**
  * `instanceof` 및 `switch` 에서 기본 타입(primitive types) 을 사용할 수 있도록 지원
  * 예를 들어, 기존에는 `switch` 문에서 `int` , `double` 등을 직접 사용할 수 없었지만, 이제는 패턴 매칭과 함께 사용할 수 있음.
* **Flexible Constructor Bodies(JEP 492, Third Preview)**
  * 생성자에서 `super(..)` 호출 전에 필드를 초기화하는 것이 가능해짐.
  * 이를 통해 상속된 클래스에서 `super(..)` 가 호출되기 전에 필드 초기화가 가능하여 오버라이딩된 메서드 호출을 더욱 안전하게 처리할 수 있음.
* **Module Import Declarations(JEP 494, Second Preivew)**
  * 하나의 `import` 문으로 모듈이 내보낸 모든 패키지를 가져올 수 있도록 지원.
  * 기존에는 패키지 단위로만 `import` 가 가능했지만, 모듈 단위로도 간편하게 가져올 수 있음.
* **Simple Source Files and Instance Main Methods(JEP 495,  Fourth Preview)**
  * 초보 개발자가 자바 프로그램을 더 쉽게 작성할 수 있도록 단순한 형태의 소스 파일과 인스턴스 메인 메서드를 지원.
  * 예를 들어, `public static void main(String[] args)` 없이도 실행 가능한 단순한 프로그램을 작성 가능

***

### 2. 성능 및 런타임 개선(Performance and Runtime)

* **Compact Object Headers(JEP 450, Experimental)**
  * 64비트 아키텍처에서 객체 헤더 크기를 기존 96 \~ 128 비트에서 64비트로 줄여서 메모리 사용량 절감 및 데이터 로컬리티 향상
* **Latte Barrier Expansion for G1(JEP 475)**
  * G1 GC 의 Barrier 확장을 최적화하여 성능 향상
* **Ahead-of-Time Class Loading & Linking(JEP 483)**
  * 애플리케이션 실행 속도를 높이기 위해 JVM 이 클래스 로딩 및 링크 정보를 캐시하여 다음 실행 시 즉시 사용 가능하도록 함.
* **Synchronize Virtual Threads without Pinning(JEP 491)**
  * `synchronized` 블록을 사용하는 가상 쓰레드(Virtual Thread) 가 플랫폼 쓰레드와 분리되어 보다 효율적으로 동작하도록 개선

***

### 3. 라이브러리 개선(Libraries)

* **Stream Gatherers(JEP 485)**
  * 기존 Stream API 에 사용자 정의 중간 연산(intermediate operation) 을 추가할 수 있도록 개선
* **Class-File API(JEP 484)**
  * Java 클래스 파일을 분석, 생성 및 변환할 수 있는 표준 API 제공
* **Scope Values(JEP 487, Fourth Preview)**
  * `ThreadLocal` 보다 가볍고 안전한 방식으로 데이터를 공유할 수 있는 새로운 스코프 값을 도입
* **Vector API(JEP 489, Ninth Incubator)**
  * 벡터 연산을 최적화하여 CPU 성능을 극대화하는 API 추가
* **Structured Concurrency(JEP 499, Fourth Preview)**
  * 병렬 프로그래밍을 더 쉽게 하기 위해 구조적 동시성(Structured Concurrency) API 제공.

***

### 4. 보안 기능 강화(Security)

* **Key Derivation Function API(JEP 478, Preview)**
  * KDF(Key Derivation Function) API 를 추가하여 암호 키를 보다 안전하게 파생 가능
* **Quantum-Resistant Module-Lattice-Based Key Encapsulation Mechanism(JEP 496)**
  * 양자 컴퓨터 공격에 대비한 새로운 암호화 키 캡슐화 메커니즘(ML-KEM) 지원
* **Quantum-Resistant Module-Lattice-Based Digital Signature Algorithm(JEP 497)**
  * 양자 저항성을 갖춘 디지털 서명 알고리즘(ML-DSA) 지원

***

### 5. 제거 및 경고

* **Java Native Interface(JNI) 제한 예고(JEP 472)**
  * 향후 JNI 사용을 제한할 예정이며, 현재는 경고 메시지를 출력
* **Security Manager 완전 제거(JEP 486)**
  * Java 17에서 삭제 예정이였던 `Security Manager` 를 완전히 비활성화.
* **ZGC 의 Non-Generationl Mode 제거(JEP 490)**
  * Z Garbage Collector(ZGC) 에서 Non-Generational 모드를 제거하고, Generational 모드만 유지
* **sun.misc.Unsafe 메모리 접근 메서드 사용 시 경고(JEP 498)**
  * `sun.misc.Unsafe` 의 메모리 접근 메서드를 사용할 경우 경고 메시지 출력

***

### JDK 24 에서 추가된 기타 새로운 기능

* Unicode 16.0 지원(`java.lang.Character` 클래스에 5185개의 새로운 문자 추가)
* Process.waitFor(Duration) 메서드 추가 (`Duration` 객체를 직접 사용 가능)
* jar 명령어 개선(JAR 파일을 특정 디렉토리로 추출하는 옵션 추가)
* Virtual Thread Scheduler 모니터링 기능(`jdk.management.VirtualThreadSchedulerMXBean` 제공)
* TLS 1.3 의 새로운 세션 티켓 개수 조정 가능(`jdk.tls.server.newSessionTicket` 속성 추가)
* Quantum-Resistant TLS 키 및 서명 알고리즘 제공(양자 내성 암호화 지원)
* OpenJDK 와 Oracle JDK 의 차이점 유지(예: GraalVM JIT 포함 여부, 설치 프로그램 지원 등)

