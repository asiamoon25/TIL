---
description: 자바 컬렉션 프레임워크
coverY: 0
---

# Java Collection FrameWork

## Java Collection Framework 란?&#x20;



Java Collection Framework 는 데이터 구조와 알고리즘을 표준화된 형태로 제공하는 라이브러리

데이터를 효율적으로 저장, 관리, 탐색, 수정 할 수 있도록 설계된 클래스 및 인터페이스의 집합임.



Java 2 부터 추가 되었으며, 다양한 컬렉션(데이터 구조)을 다루기 위해 재사용 가능한 자료구조와 알고리즘을 제공함.



### 특징

1. 표준화된 설계
   1. 여러 데이터 구조와 알고리즘이 일관된 방식으로 설계되어 코드의 가독성과 유지보수를 쉽게함.
2. 다양한 데이터 구조 지원
   1. 배열, 리스트, 집합(Set), 맵(Map), 큐(Queue) 등 다양한 자료구조를 제공함.
3. 재사용 가능
   1. 인터페이스를 구현하여 확장 가능하며, 기본적으로 제공되는 클래스를 재사용할 수 있음.
4. 알고리즘 내장
   1. 정렬, 검색, 병합과 같은 기본적인 알고리즘을 지원하는 유틸리티 클래스(\`Collections\`) 를 제공함.
5. 동기화 및 비동기화 지원
   1. 멀티스레드 환경에서도 사용할 수 있도록 동기화된 컬렉션 생성 기능이 포함되어 있음.
6. 제네릭 지원
   1. Java 5 부터 제네릭(Generic) 을 지원하여 타입 안정성과 가독성을 향상 시켰음.



***



## Collection Framework 의 주요 구성 요소

Java Collection Framework 는 크게 두 가지로 나뉨.

1. Collection 인터페이스 계열 : 데이터를 그룹으로 관리
2. Map 인터페이스 계열 : key - value 쌍으로 데이터를 관리



### 1. Collection 인터페이스 계열

Collection 인터페이스는 데이터를 그룹으로 저장하며, 이를 구현한 주요 하위 인터페이스로 `List`, `Set`, `Queue`가 있음.



#### 1-1 List (순서 유지, 중복 허용)

* 특징 : 순서를 유지하며, 요소의 중복을 허용함.
* 구현 클래스:
  * `ArrayList`: 동적 배열 기반, 조회 속도가 빠름.
  * `LinkedList`: 연결 리스트 기반, 삽입/삭제가 빠름.
  * `Vector`: 동기화 지원, 현재는 잘 사용되지 않음.
  * `Stack`: LIFO 방식의 자료구조

#### 1-2 Set(중복 허용 안함, 순서 무관)

* 특징 : 중복을 허용하지 않으며, 순서가 보장되지 않음.
* 구현 클래스:
  * `HashSet`: 빠른 탐색이 가능한 해시 기반 Set.
  * `LinkedHashSet`: 삽입 순서를 유지
  * `TreeSet`: 요소를 정렬된 상태로 유지

#### 1-3 Queue (FIFO, 선입선출)

* 특징 : 요소를 큐 구조로 관리, 주로 순차적인 작업 처리에 사용.
* 구현 클래스:
  * `PriorityQueue`: 우선순위에 따라 요소를 관리
  * `LinkedList`: 큐로도 사용 가능
* Deque( 양방향 큐 ):
  * `ArrayList`: 양쪽에서 삽입/삭제가 가능한 큐
  * `LinkedList`: Deque 로도 사용 가능



### 2. Map 인터페이스 계열

Map 은 키와 값의 쌍(Key-Value Pair) 으로 데이터를 저장함. 키는 중복을 허용하지 않으며, 값은 중복이 가능함.

* 구현 클래스 :&#x20;
  * `HashMap`: 해시 기반으로 빠른 검색 및 저장
  * `LinkedHashMap`: 삽입 순서를 유지
  * `TreeMap`: 키를 정렬된 순서로 유지
  * `Hashtable`: 동기화를 지원, 현재는 잘 사용되지 않음.
  * `WeakHashMap`: 키에 약한 참조를 사용하여 GC 가 수집 가능
  * `IdentityHashMap`: 키의 참조 동일성(identity) 으로 비교

***

## Collection Framework 의 구조

Java Collection Framework 는 다음과 같은 구조로 이루어져 있음.

```
java.util.Collection
   ├── List
   │    ├── ArrayList
   │    ├── LinkedList
   │    ├── Vector
   │         └── Stack
   ├── Set
   │    ├── HashSet
   │    ├── LinkedHashSet
   │    └── TreeSet
   └── Queue
        ├── PriorityQueue
        ├── ArrayDeque
        └── LinkedList (Queue 및 Deque로 사용 가능)

java.util.Map
   ├── HashMap
   ├── LinkedHashMap
   ├── TreeMap
   ├── Hashtable
   ├── WeakHashMap
   └── IdentityHashMap

```

***

## 유틸리티 클래스



### 1. Collection 클래스

* 정렬 :  \`Collections.sort(List\<T>)\`
* 탐색 : \`Collections.binarySearch(List\<T>, T key)\`
* 동기화 : \`Collections.synchronizedList(List\<T>)\`
* 읽기 전용 : \`Collections.unmodifiableList(List\<T>)\`



### Array 클래스

* 배열을 리스트로 변환 : \`Arrays.asList(T... a)\`
* 배열 복사 : \`Arrays.copyOf(T\[] original, int newLength)\`
* 배열 정렬 : \`Arrays.sort(T\[] a)\`

***



## Collection Framework 주의사항

1. 적합한 자료구조 선택
   1. 데이터의 크기, 삽입/삭제 빈도, 검색 속도 요구사항에 따라 적합한 자료구조를 선택
2. 타입 안정성
   1. 제네릭을 사용하여 타입 안정성을 확보
3. 멀티스레드 환경
   1. 동기화가 필요한 경우, `` Collections.synchonizedXXX` ``  메서드로 동기화된 컬렉션을 생성하거나, `ConcurrentHashMap` 과 같은 동시성 컬렉션을 사용



