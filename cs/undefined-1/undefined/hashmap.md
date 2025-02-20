# HashMap

### Java HashMap 은 어떻게 동작하는가

Java 의 `HashMap` 은 **키-값(key-value)** 구조의 자료구조로, 내부적으로 **배열(Array)** 과 **연결 리스트(LinkedList)**, **트리(Tree)** 를 조합하여 구현되어 있음.

* 평균적으로, $$O(1)$$의 시간 복잡도로 데이터를 저장하고 검색할 수 있음.
* **중복된 키를 허용하지 않고, 값은 중복될 수 있음**

***

### 1. HashMap 의 내부 구조&#x20;

`HashMap` 은 내부적으로 **배열 + 연결 리스트 + 트리** 로 구성됨.

#### 기본적인 저장 구조

1. 데이터를 저장할 때, **해시 함수(Hash Function)** 을 이용해 키를 해시 값(Hash Code) 으로 변환
2. 이 해시 값을 기반으로 **배열의 특정 인덱스(Bucket) 에 저장**
3. **같은 인덱스에 충돌(Collision) 이 발생하면, 연결 리스트 또는 트리(자바 8이상) 로 저장**&#x20;

```java
HashMap<String, Integer> map = new HashMapM<();
map.put("apple", 10);
map.put("banana", 20);
map.put("cherry", 30);
```

위 코드에서 `"apple"` 의 해시 값이 배열의 특정 인덱스(예: `5번 인덱스` ) 로 변환되면, 해당 위치에 `("apple", 10)` 값이 저장됨.

***

### 2. 해시 충돌(Hash Collision) 해결 방법

해시 충돌이 발생하면, 같은 해시 값을 갖는 여러 개의 키가 같은 버킷(Index)에 저장될 수 있음.

이 경우, **Java 8 이전과 이후의** `HashMap` **은 충돌을 처리하는 방식이 다름.**

:large\_blue\_diamond: **Java 8 이전(** `Linked List` **이용)**

* 동일한 해시 값이 나오면, **해당 인덱스의 연결 리스트(Linked List)** 끝에 새로운 노드 추가.
* 충돌이 많아지면 **검색 성능이 O(N) 까지 떨어질 수 있음**

:large\_blue\_diamond: **Java 8 이후(** `Tree 구조 도입` **)**

* 충돌이 일정 개수(8개 이상) 이상 발생하면, 해당 버킷을 `Red-Black Tree(레드-블랙 트리)` 로 변경&#x20;
* **트리 구조는 O(log N) 의 성능을 가지므로 검색 성능이 향상됨.**

```java
// Java 8 이후, 트리 변환 예제 
HashMap<String, Integer> map = new HashMap<>();
for(int i = 0; i < 10; i++) {
    map.put("key" + i, i);
}
```

***

### 3. HashMap 의 주요 메서드 동작 원리&#x20;

:large\_blue\_diamond: `put(K key, V value)`&#x20;

1. **키의** `hashCode()` **를 호출하여 해시 값 계산**
2. 해시 값에 따라 **배열의 인덱스(Bucket) 결정**
3. **비어 있으면 새로운 노드 추가, 이미 값이 있으면 연결 리스트/트리에 저장**

```java
map.put("dog", 100); // "dog"의 해시 값을 계산하여 특정 위치에 저장
```

:large\_blue\_diamond: `get(K key)`&#x20;

1. **키의** `hashCode()` **를 호출하여 해시 값 계산**
2. 해당 해시 값을 기반으로 배열의 인덱스를 찾음.
3. 해당 인덱스에 **키가 존재하면 값을 반환**, 충돌이 있다면 연결 리스트 또는 트리에서 검색&#x20;

```java
System.out.println(map.get("dog")); // 100
```

:large\_blue\_diamond: `remove(K key)`

1. **키의 해시 값을 계산하여 해당 버킷을 찾음.**
2. 키가 존재하면 해당 값을 **연결 리스트 또는 트리에서 삭제**

```java
map.remove("dog");
```

***

### 4. HashMap 의 동적 크기 조절(Rehashing)

`HashMap` 의 기본 크기(초기 버킷 수) 는 **16** 이며 `load factor(기본 0.75)` 를 초과하면 **배열 크기를 2배로 증가(Resizing)** 시킴 .

:large\_blue\_diamond: **리사이징(Resizing) 과정**

1. **배열 크기가 두 배 증가** (예: 16 ⇒ 32)
2. **모든 기존 키의 해시 값을 다시 계산하여 새로운 버킷에 배치**
3. **연결 리스트에 저장된 노듣들도 새롭게 재배치**

```java
HashMap<String, Integer> map = new HashMap<>(4, 0.75f); // 초기 크기 4, load factor 0.75
map.put("a",1);
map.put("b",2);
map.put("c",3);
map.put("d",4); // 이때, 크기가 8로 증가(Rehashing 발생)
```

***

### 5. HashMap 과 HashTable 의 차이&#x20;

| 비교 항목            | **HashMap**       | **Hashtable**    |
| ---------------- | ----------------- | ---------------- |
| **동기화 지원**       | ❌ (비동기)           | ✅ (동기화 지원)       |
| **멀티스레드 환경**     | ❌ 스레드 안전 X        | ✅ 스레드 안전         |
| **성능**           | ✅ 빠름              | ❌ 느림             |
| **`null` 허용 여부** | ✅ (`null` 키/값 허용) | ❌ (`null` 허용 안함) |

**📌 멀티스레드 환경에서는 `ConcurrentHashMap`을 사용하면 좋음.**
