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
