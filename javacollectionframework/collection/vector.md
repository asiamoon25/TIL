# Vector

## Vector



### 1.1 개요

* `Vector` 는 **동기화된(synchronized)** `List` 구현체임
* 내부적으로는 동적으로 크기를 조절할 수 있는 배열을 사용하여 데이터를 저장함.
* Java 1.2 부터는 `ArrayList` 와 함께 `List` 인터페이스를 구현하며, 레거시 클래스로 간주됨.
* 동기화가 필요하지 않은 경우 `ArrayList` 가 더 효율적임



### 1.2 주요 특징

1. 동기화 지원
   1. 멀티스레드 환경에서 안전하게 사용할 수 있도록 동기화를 지원함.
   2. 그러나, 동기화로 인해 단일 스레드 환경에서는 성능이 떨어질 수 있음.
2. 초기 크기와 증가 용량
   1. 기본 크기 : 1.0
   2. 용량이 초과되면 현재 크기의 2배로 증가함.
3. 순차적인 접근
   1. 인덱스를 사용하여 데이터를 추가하거나 검색할 수 있음.



### 1.3 주요 메서드&#x20;

`Vector` 는 `List` 인터페이스를 구현하므로 `ArrayList` 와 유사한 메서드를 제공함.

* `add(E element)` : 요소를 추가함.
* `add(int index, E element)`: 지정된 위치에 요소를 삽입함.
* `remove(Object o)` : 객체를 제거함.
* `get(int index)` : 특정 인덱스의 요소를 반환함.
* `size()` : 저장된 요소의 수를 반환함.
* `capacity()`: 내부 배열의 현재 크기를 반환함.
* `trimToSize()`: 용량을 현재 요소 개수에 맞게 줄임.

***

## Stack

### 2.1 개요

* `Stack` 은 `Vector` 를 상속받아 구현된 클래스임.
* **LIFO(Last-In-First-Out)** 방식으로 동작하는 컬렉션임.
* 동기화가 지원되므로 멀티스레드 환경에서 안전하게 사용할 수 있음.



### 2.2 주요 특징

1. LIFO 구조:
   1. 가장 마지막에 삽입된 요소가 가장 먼저 삭제됨.
   2. 스택에 데이터를 쌓거나 제거할 때 `push` 와 `pop` 메서드를 사용함.
2. 레거시 클래스
   1. Java 1.2 부터 등장했으며, 최신 환경에서는 `Deque`(특히 `ArrayDeque`)를대체재로  사용하기도함.&#x20;
3. 동기화
   1. 멀티스레드 환경에서 사용 가능하지만, `Vector` 와 동일한 성능 저하 문제가 있음.



### 2.3 주요 메서드

* `push(E item)`: 스택의 맨 위에 요소를 추가
* `pop()`: 스택의 맨 위에 있는 요소를 제거하고 반환
* `peek()`: 스택의 맨 위 요소를 반환하지만, 제거하지는 않음
* `empty()`: 스택이 비어 있는지 확인
* `search(Object o)`: 특정 객체의 1-based 위치를 반환 (스택 맨 위가 1번)



***



## Vector vs Stack

| **동작 구조** | 동적 배열 | LIFO 구조 |
| --------- | ----- | ------- |

| **메서드** | `add`, `remove`, `get` 등 | `push`, `pop`, `peek` 등 |
| ------- | ------------------------ | ----------------------- |

| **스레드 안전성** | 동기화 지원 | 동기화 지원 |
| ----------- | ------ | ------ |

| **사용 용도** | 일반적인 리스트 구현 | LIFO 스택 구조 필요 시 |
| --------- | ----------- | --------------- |



***



## 사용 예시

### Vector

```java
import java.util.Vector;

public class VectorExample {
    public static void main(String[] args) {
        Vector<String> vector = new Vector<>();
        
        vector.add("A");
        vector.add("B");
        vector.add("C");
        
        vector.add(1, "D"); // index 0 에 "D" 삽입
        
        // 요소 출력
        System.out.println("Vector : " + vector);
        
        // 요소 제거
        vector.remove("B");
        System.out.println("After removal: " + vector);
        
        // 용량 확인
        System.out.println("Capacity: " + vector.capacity());
    }
}
```



### Stack

```java
import java.util.Stack;

public class StackExample {
    public static void main(String[] args) {
        Stack<Integer> stack = new Stack<>();

        // 스택에 요소 추가
        stack.push(10);
        stack.push(20);
        stack.push(30);

        // 스택 맨 위 요소 확인
        System.out.println("Peek: " + stack.peek());

        // 스택에서 요소 제거
        System.out.println("Pop: " + stack.pop());
        System.out.println("Stack after pop: " + stack);

        // 스택이 비었는지 확인
        System.out.println("Is stack empty? " + stack.empty());
    }
}
```

