---
description: List 관련
---

# List

## 1. ArrayList&#x20;

* 동적 배열 기반
* 조회 속도가 빠름
* 삽입/삭제가 느림(특히 중간 삽입/삭제 시)

```java
List<String> arrayList = new ArrayList<>();

// 요소 추가
arrayList.add("Apple");
arrayList.add("Banana");
arrayList.add("Cherry");
// 요소 출력
System.out.println("ArrayList: " + arrayList);
// 특정 위치에 요소 삽입
arrayList.add(1, "Blueberry");
System.out.println("After insertion: " + arrayList);
// 특정 요소 가져오기
String fruit = arrayList.get(2);
System.out.println("Element at index 2: " + fruit);
// 요소 제거
arrayList.remove("Banana");
System.out.println("After removal: " + arrayList);
// 요소 변경
arrayList.set(1, "Blackberry");
System.out.println("After update: " + arrayList);
// 리스트 크기
System.out.println("Size of list: " + arrayList.size());
```

## 2. LinkedList

* 노드 기반 연결 리스트
* 삽입/삭제가 빠름
* 조회 속도가 느림.

```java
import java.util.LinkedList;
import java.util.List;

public class LinkedListExample {
    public static void main(String[] args) {
        List<String> linkedList = new LinkedList<>();

        // 요소 추가
        linkedList.add("Dog");
        linkedList.add("Cat");
        linkedList.add("Rabbit");

        // 요소 출력
        System.out.println("LinkedList: " + linkedList);

        // 첫 번째와 마지막에 요소 추가
        ((LinkedList<String>) linkedList).addFirst("Horse");
        ((LinkedList<String>) linkedList).addLast("Elephant");
        System.out.println("After adding first and last: " + linkedList);

        // 특정 위치 요소 제거
        linkedList.remove(2);
        System.out.println("After removal: " + linkedList);

        // 리스트 순회
        for (String animal : linkedList) {
            System.out.println("Animal: " + animal);
        }
    }
}

```

## 3. Vector

* 동기화된 동적 배열
* 멀티스레드 환경에서 안전하지만, 성능은 느림.
* 현재 잘 사용되지 않음( ArrayList + 동기화)

```java
import java.util.Vector;

public class VectorExample {
    public static void main(String[] args) {
        Vector<Integer> vector = new Vector<>();

        // 요소 추가
        vector.add(10);
        vector.add(20);
        vector.add(30);

        // 요소 출력
        System.out.println("Vector: " + vector);

        // 요소 삽입
        vector.add(1, 15);
        System.out.println("After insertion: " + vector);

        // 요소 제거
        vector.remove((Integer) 20);
        System.out.println("After removal: " + vector);

        // 리스트 크기와 용량
        System.out.println("Size: " + vector.size());
        System.out.println("Capacity: " + vector.capacity());
    }
}

```

### 3-1. Stack

* `Vector` 를 기반으로 구현된 클래스
* LIFO 방식의 자료구조

```java
import java.util.Stack;

public class StackExample {
    public static void main(String[] args) {
        Stack<String> stack = new Stack<>();

        // 요소 추가 (push)
        stack.push("First");
        stack.push("Second");
        stack.push("Third");

        // 스택 출력
        System.out.println("Stack: " + stack);

        // 맨 위 요소 확인
        System.out.println("Peek: " + stack.peek());

        // 맨 위 요소 제거
        System.out.println("Pop: " + stack.pop());
        System.out.println("After pop: " + stack);

        // 스택이 비어 있는지 확인
        System.out.println("Is stack empty? " + stack.isEmpty());
    }
}
```



***



## ArrayList vs LinkedList vs Vector

| **기반 구조** | 동적 배열 | 이중 연결 리스트 | 동적 배열 |
| --------- | ----- | --------- | ----- |

| **속도** | 조회 빠름, 삽입 느림 | 삽입/삭제 빠름 | 조회는 빠름 |
| ------ | ------------ | -------- | ------ |

| **동기화** | 지원하지 않음 | 지원하지 않음 | 동기화 지원 |
| ------- | ------- | ------- | ------ |

| **사용성** | 일반적으로 많이 사용 | 특정 상황에 적합 | 잘 사용되지 않음 |
| ------- | ----------- | --------- | --------- |

***

## 멀티스레드 환경에서의 List

멀티스레드 환경에서 동기화된 리스트가 필요하다면 다음과 같이 `Collections.synchronizedList`  를 사용하면 된다.

```java
import java.util.ArrayList;
import java.util.Collections;
import java.util.List;

public class SynchronizedListExample {
    public static void main(String[] args) {
        List<String> list = Collections.synchronizedList(new ArrayList<>());

        list.add("Thread-Safe");
        list.add("List");

        synchronized (list) {
            for (String item : list) {
                System.out.println(item);
            }
        }
    }
}

```

