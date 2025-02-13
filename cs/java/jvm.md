# JVM

**Java 애플리케이션을 실행하기 위한 가상 머신** 으로, Java 코드 (바이트코드) 를 실행하는 역할을 함.

**플랫폼 독립성** 을 제공하며, Java 프로그램이 다양한 운영체제에서 실행될 수 있도록 해줌.

***

### 1. JVM 의 주요 기능

#### 1.1 플랫폼 독립성 제공

* Java 코드는 `.class` 파일(바이트코드)로 변환됨.
* JVM이 해당 바이트코드를 실행하여 운영체제(OS)와 상관없이 실행 가능
* "Write Once, Run Anywhere(WORA)" 개념 구현

#### 1.2 메모리 관리(Garbage Collection)

* JVM 은 **Heap 메모리를 자동으로 관리**
* `Garbage Collector(GC)` 가 불필요한 객체를 제거하여 메모리를 최적화

#### 1.3 실행 최적화(JIT 컴파일러)

* Java 코드는 처음에는 바이트코드로 실행되지만, JVM 의 **JIT(Just-In-Time) 컴파일러** 가 실행 중 일부 바이트코드를 네이티브 코드로 변환하여 성능 향상

***

### 2. JVM 의 주요 구성 요소

JVM 은 크게 3가지 주요 컴포넌트로 구성됨.

#### 2.1 클래스 로더 시스템(Class Loader System)

* **클래스를 메모리에 로드하고 링크, 초기화하는 역할**
* Java 코드(`.java` ) ⇒ 바이트코드(`.class` ) 변환 후 JVM 내부에서 실행됨.

**클래스 로딩 과정**

1. **로딩(Loading)** : `.class` 파일을 읽고 메모리에 로드
2. **랭킹(Ranking) :**&#x20;
   1. **검증(Verification)** : 바이트코드가 올바른지 확인&#x20;
   2. **준비(Preparation)** : 정적 변수 메모리 할당
   3. **해석(Resolution)** : 심볼릭 참조를 실제 참조로 변환
3. **초기화(Initialization)** : `static` 변수 초기화 및 `static` 블록 실행

**클래스 로더 유형**

* **Bootstrap ClassLoader** : `java.lang` 패키지 등 핵심 라이브러리 로드
* **Extension ClassLoader** : `ext` 디렉터리 라이브러리 로드
* **Application ClassLoader** : 클래스패스에서 사용자 클래스 로드

#### 2.2 런타임 데이터 영역(Runtime Data Area)

JVM의 메모리 구조는 여러 영역으로 나뉨

**JVM 메모리 구조**

1. **Method Area(메서드 영역)**
   1. 클래스 정보, 정적 변수, 런타임 상수 풀을 저장
   2. 모든 쓰레드가 공유(Static Data 저장)
2. **Heap Area(힙 영역)**
   1. 객체와 인스턴스 변수가 저장되는 공간
   2. 모든 쓰레드가 공유
   3. Garbage Collector(GC) 관리 대상
3. **Stack Area(스택 영역)**
   1. 각 쓰레드마다 생성되며, 메서드 호출 시 스택 프레임이 생성됨.
   2. 지역 변수, 메서드 호출 정보 저장
4. **PC Register(PC 레지스터)**
   1. 현재 실행 중인 **JVM 명령어의 주소 를** 저장
   2. 각 쓰레드마다 별도로 존재
5. **Native Method Stack(네이티브 메서드 스택)**
   1. JNI(Java Native Interface) 기반의 네이티브 코드(C/C++ 등) 실행을 위한 공간

#### 2.3 실행 엔진(Execution Engine)

JVM 에서 실제로 바이트코드를 실행하는 컴포넌트

**실행 과정**

1. **인터프리터**
   1. 바이트코드를 한줄 씩 읽어 해석하고 실행
   2. 실행 속도가 느리지만, 즉시 실행 가능
2. **JIT(Just-In-Time) 컴파일러**
   1. 인터프리터의 단점을 보완하기 위해 **자주 실행되는 코드 를** 네이티브 코드로 변환하여 속도를 향상
   2. HotSpot JVM 에서 활용
3. **Garbage Collector(GC)**
   1. Heap 영역의 불필요한 객체를 자동으로 제거하여 메모리를 최적화

***

### 3. JVM 의 실행 과정

#### Java 프로그램 실행 흐름

1. **소스 코드 작성**(`.java` 파일)
2. **컴파일**(`javac` 사용하여 `.class` 바이트코드 생성)
3. **JVM 이 바이트코드 로드**(`Class Loader` 를 통해 메모리로 로드)
4. **바이트코드 실행** (`Execution Engine` 이 인터프리터/JIT 로 실행)
5. **Garbage Collector가 메모리 관리**

***

### 4. JVM 과 JDK, JRE 의 차이점

| **구성 요소**                          | **설명**                          |
| ---------------------------------- | ------------------------------- |
| **JVM (Java Virtual Machine)**     | 바이트코드를 실행하는 가상 머신               |
| **JRE (Java Runtime Environment)** | JVM + 기본 라이브러리 (Java 실행 환경)     |
| **JDK (Java Development Kit)**     | JRE + Java 개발 도구 (컴파일러, 디버거 포함) |

**JVM → JRE → JDK 순으로 포함 관계가 있음.**\
즉, JDK를 설치하면 JRE와 JVM이 포함됨.

***

### 5. JVM 튜닝 및 최적화

JVM 은 기본적으로 자동 메모리 관리를 제공하지만, 성능을 최적화하기 위해 다양한 설정이 가능함.

**JVM 옵션 예제**

```bash
java -Xms512m -Xmx2g -XX:+UseG1GC -XX:MaxGCPauseMillis=200
```

| `-Xms512m` | 초기 Heap 크기 512MB 설정 |
| ---------- | ------------------- |

| `-Xmx2g` | 최대 Heap 크기 2GB 설정 |
| -------- | ----------------- |

| `-XX:+UseG1GC` | G1 Garbage Collector 사용 |
| -------------- | ----------------------- |

| `-XX:MaxGCPauseMillis=200` | GC 최대 정지 시간 200ms로 제한 |
| -------------------------- | --------------------- |

***

### 6. JVM Garbage Collection (GC)

JVM 은  **GC(Garbage Collection)** 을 통해 Heap 메모리를 자동 관리함.

#### GC 주요 알고리즘

1. **Serial GC** ⇒ 단일 쓰레드에서 GC 수행(작은 애플리케이션에 적합)
2. **Parallel GC** ⇒ 멀티 쓰레드로 GC 수행(멀티코어 환경에 적합)
3. **CMS GC(Concurrent Mark Sweap)** ⇒ 응답 속도가 중요한 서비스에서 사용
4. **G1 GC(Garbage First GC)** ⇒ 최신 GC, 대규모 애플리케이션에 최적화

#### GC 동작 방식

1. **Young Generation(Eden + Survivor)**
   1. 새롭게 생성된 객체가 저장됨.
   2. **Minor GC** 발생 시 살아남은 객체는 Survivor 로 이동
2. **Old Generation(Tenured)**
   1. Young Generation 을 지나 오래된 객체가 저장됨.
   2. **Major GC/ Full GC** 발생 시 메모리 회수
3. **Permanent Generation(Metaspace)**
   1. 클래스 정보와 메타데이터 저장(Java 8 이후 Metaspace로 변경)

