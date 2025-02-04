# Memory

운영체제(OS) 에서 메모리 관리는 시스템의 안정성과 성능을 보장하는 핵심 요소임.

메모리 관리는 여러 가지 기법을 사용하여 프로세스가 필요로 하는 메모리를 할당하고 해체하며, 효율적으로 활용할 수 있도록 함.

***

### 1. 메모리 계층 구조(Memory Hierarchy)

메모리는 속도와 크기에 따라 계층적으로 구성됨. 일반적으로 다음과 같은 구조를 가짐.

1. **레지스터(Register)** : CPU 와 RAM 사이에 위치하며, 가장 빠른 속도를 가짐.(나노초 단위)
2. **캐시(Cache Memory)** : CPU와 RAM 사이에 위치하며, L1, L2, L3 캐시로 구분됨.
3. **주 메모리(Main Memory, RAM)** : 실행 중인 프로세스가 직접 사용하는 공간.
4. **가상 메모리(Virtual Memory)** : 물리적 메모리가 부족할 때 디스크를 메모리처럼 사용.
5. **보조 저장장치(Storage, HDD/SDD)** : 장기 저장 용도로 사용되며, 접근 속도가 가장 느림.

***

### 2. 주소 공간과 주소 변환(Addressing & Translation)

프로세스가 사용하는 주소는 **논리 주소(Logical Address)**  또는 **가상 주소(Virtual Address)** 이며, 실제 메모리에서 위치인 **물리 주소(Physical Address)** 로 변환됨.&#x20;

이를 담당하는 장치가 **메모리 관리 유닛(MMU, Memory Management Unit)** 임.

#### 주소 바인딩(Address Binding)

주소 바인딩은 논리 주소를 물리 주소로 변환하는 방식으로, 다음과 같이 나뉨.

1. **컴파일 타임 바인딩(Compile Time Binding)**
   1. 프로그램이 컴파일될 때 주소가 결정됨.
   2. 실행 중에 위치 변경이 불가능함.
2. **로드 타임 바인딩(Load Time Binding)**
   1. 프로그램이 로드될 때 실제 주소가 결정됨.
   2. 실행 전이라면 재배치 가능
3. **실행 시간 바인딩(Execution Time Binding)**
   1. 프로그램이 실행 중에도 주소가 동적으로 변경될 수 있음.
   2. 가상 메모리에서 사용됨.

#### 주소 변환 방식

* **베이스(Base) 레지스터 + 한계(Limit) 레지스터 방식** : 프로세스의 시작 주소와 크기를 관리
* **페이지 테이블(Page Table)** : 논리 주소를 물리 주소로 변환
* **세그먼트 테이블(Segment Table)** : 세그먼트 단위의 메모리 관리를 수행

***

### 3. 연속 메모리 할당(Contiguos Memory Allocation)

프로세스에 연속된 메모리 블록을 할당하는 기법임.

#### 고정 분할(Fixed Partitioning)

* 메모리를 일정한 크기로 분할하여 관리
* 내부 단편화(Internal Fragmentation) 발생 가능

#### 가변 분할(Variable Partitioning)

* 프로세스 크기에 맞게 메모리를 동적으로 할당.
* 외부 단변화(External Fragmentation) 발생 가능

#### 단편화(Fragmentation)

* **내부 단편화(Internal Fragmentation)** : 프로세스가 할당받은 공간보다 작은 크기를 사용하여 메모리가 낭비됨.
* **외부 단편화(External Fragmentation)** : 가변 크기의 메모리 할당 후 빈 공간이 조각나서 사용하지 못하는 공간이 발생

#### 해결방법

* **압축** : 메모리의 빈 공간을 한 곳으로 모아 큰 공간을 확보
* **페이징** 또는 **세그멘테이션(Segmentation)** 사용.

***

### 4. 페이징 과 세그멘테이션

연속된 메모리 할당의 단점(외부 단편화)을 해결하기 위해 비연속적인 메모리 할당 기법이 사용됨.

#### 페이징(Paging)

* 메모리 고정 크기(페이지 단위)로 나누어 관리
* 페이지 테이블(Page Table) 을 사용하여 논리 주소를 물리 주소로 변환.
* 외부 단편화가 발생하지 않지만, 내부 단편화 가능

<mark style="background-color:purple;">페이지 테이블(Page Table) 구조</mark>

* **기본 페이지 테이블** : 프로세스마다 별도의 테이블을 유지
* **다단계 페이지 테이블(Multi-level Page Table)** : 페이지 테이블이 너무 클 경우 계층적으로 저장
* **TLB(Translation Lookaside Buffer)** : 페이지 테이블 조회 속도를 높이기 위한 캐시

#### 세그멘테이션(Segmentation)

* 프로세스를 논리적인 단위(코드, 데이터, 스택 등)로 나누어 메모리를 관리
* 세그먼트 테이블(Segment Table) 을 사용하여 변환
* 외부 단편화가 발생할 수 있음.

***

### 5. 가상 메모리(Virtual Memory)

가상 메모리는 실제 RAM 보다 더 큰 메모리 공간을 제공하는 기술임. 프로세스는 전체 프로그램을 로드하지 않고 필요한 부분만 실행함.

#### 페이지  교체(Page Replacement) 알고리즘

가상 메모리에서 페이지가 가득 차면, 오래된 페이지를 제거하고 새로운 페이지를 로드해야 함.

1. FIFO(First-In-First-Out) : 가장 먼저 들어온 페이지를 제거
2. LRU(Least Recently Used) : 가장 오래 사용되지 않은 페이지를 제거
3. Optimal(OPT) : 앞으로 가장 오랫동안 사용되지 않을 페이지를 제거
4. LFU(Least Frequently Used) : 가장 적게 사용된 페이지를 제거

***

### 6. 스와핑(Swapping)

* 실행 중인 프로세스를 디스크에 저장하고 필요할 때 다시 불러오는 기법
* 메모리 부족 시 사용되며, **컨텍스트 스위칭(Context Switching)** 비용이 발생

***

### 7. 캐시 메모리(Cache Memory)

CPU 와 메모리 간 속도 차이를 줄이기 위해 사용되는 고속 메모리

#### 캐시 매핑 방식

* 직접 매핑(Direct Mapping) : 메모리의 특정 블록을 특정 캐시 라인에 매핑
* 연관 매핑(Associative Mapping) : 모든 캐시 라인에 데이터를 저장 가능
* 세트 연관 매핑(Set-Associative Mapping) : 직접 매핑과 연관 매핑의 절충안

***

### 8. 메모리 보호(Memory Protection)

운영체제는 각 프로세스가 자신의 메모리만 접근할 수 있도록 보호함.

#### 메모리 보호 기법

* **베이스(Base) 와 리미트(Limit) 레지스터** 사용
* 페이지 보호 비트(Protcetion Bit) 활용.
* MMU와 커널 모드(Kernel Mode) 지원

***

### 9. NUMA(Non-Uniform Memory Access)

* 멀티코어 및 멀티 프로세서 시스템에서 각 CPU가 로컬 메모리를 가지며, 비균일한 메모리 접근 속도를 가짐.
* NUMA 시스템에서는 **프로세스** 가 **로컬 메모리 를 우선적으로 사용** 하여 성능을 최적화 함.

