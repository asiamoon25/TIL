---
description: LinkedList
---

# LinkedList

## LinkedList



### LinkedList 의 종류

1. 단일 연결 리스트(Singly Linked List)
   1. 각 노드가 **다음 노드** 에 대한 참조만 가지고 있음.
   2. 데이터는 한 방향으로만 탐색 가능함.
2. 이중 연결 리스트(Doubly Linked List)
   1. 각 노드가 **이전 노드** 와 **다음 노드** 에 대한 참조를 가지고 있음.
   2. 양방향으로 탐색이 가능함.
3. 원형 연결 리스트(Circular Linked List)
   1. 마지막 노드가 첫 번째 노드를 가리켜서 연결이 순환 구조를 이룸.

***

### LinkedList 의 특징

#### 1. 장점

* 동적 크기 조정 : 배열과 달리 고정 크기가 필요 없으며, 노드를 추가하거나 삭제할 때마다 크기를 조정할 수 있음.
* 빠른 삽입/삭제 : 중간에 데이터를 추가하거나 제거할 때 효율적. (노드 참조만 변경하면 됨)

#### 2. 단점

* 느린 탐색 : 특정 위치에 있는 데이터를 찾으려면 처음부터 순차적으로 접근해야함.
* 추가적인 메모리 사용 : 각 노드가 데이터를 저장하는 외에도 **참조(포인터)** 를 저장해야 하므로 메모리를 더 많이 사용한다.



***

### 구성

* **노드(Node)** : 데이터와 다음 노드에 대한 참조를 포함하는 기본 단위
* **헤드(Head)** : 리스트의 첫 번째 노드를 가리키는 포인터
* **테일(Tail)** : 리스트의 마지막 노드를 가리키는 포인터(필요한 경우)

***

### 예시

#### 1. 단일 연결 리스트 구현(Java)

```java
Class Node {
    int data;
    Node next;
    
    Node(int data) {
        this.data = data;
        this.next = null;
    }
}

class LinkedList {
    Node head; // 리스트의 시작점
    
    // 노드 추가
    void add(int data) {
        Node newNode = new Node(data);
        if(head == null) {
            head = newNode;
        } else {
            Node current = head;
            while(current.next != null) {
                current = current.next;
            }
            current.next = newNode;
        }
    }
    
    // 노드 출력
    void printList() {
        Node current = head;
        while(current != null) {
            System.out.print(current.data + " -> ");
            current = current.next;
        }
        System.out.println("null");
    }
}

public class Main {
    public static void main(String[] args) {
        LinkedList list = new LinkedList();
        list.add(10);
        list.add(20);
        list.add(30);
        
        list.printList(); // result : 10 -> 20 -> 30 -> null
    }
}

```

#### 2. 이중 연결 리스트 구현(Java)

```java
class DoublyNode {
    int data;
    DoublyNode prev; // 이전 노드를 가리킴
    DoublyNode next; // 다음 노드를 가리킴
    
    DoublyNode(int data) {
        this.data = data;
        this.prev = null;
        this.next = null;
    }
}

class DoublyLinkedList {
    DoublyNode head;
    
    // 노드 추가
    void add(int data) {
        DoublyNode newNode = new DoublyNode(data);
        if(head == null) {
            head = newNode;
        } else {
            DoublyNode current = head;
            while(current.next != null) {
                current = current.next;
            }
            current.next = newNode;
            newNode.prev = current;
        }
    }
    
    // 순방향 출력
    void printForward() {
        DoublyNode current = head;
        while(current != null) {
            current = current.next;
        }
        
        while(current != null) {
            System.out.println(current.dat + " -> ");
            current = current.prev;
        }
        System.out.println("null");
    }
}

public class Main{
    public static void main(String[] args) {
        DoublyLinkedList list = new DoublyLinkedList();
        list.add(10);
        list.add(20);
        list.add(30);
        
        list.printForward(); // result : 10 -> 20 -> 30 -> null
        list.printBackward(); // result : 30 -> 20 -> 10 -> null
    }
}
```
