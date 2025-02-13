# Remove Duplicates from Sorted List

{% embed url="https://leetcode.com/problems/remove-duplicates-from-sorted-list/description/" %}

이 문제는 **정렬된 연결 리스트에서 중복된 값을 제거하는 문제** 로, 한 번의 순회로 해결할 수 있음.

***

#### 해결 방법(투 포인터)

1. **현재 노드(curr) 와 다음 노드(curr.next) 를 비교**
   1. 만약 `curr.val == curr.next.val` 이면,  `curr.next` 를 건너뛰어 중복을 제거
   2. 중복이 아니라면 `curr` 을 다음 노드로 이동
2. **순자척으로 리스트 끝까지 반복** 하면서 중복을 제거

:hourglass:**시간 복잡도** : `O(N)`&#x20;

:pushpin:**공간 복잡도** : `O(1)` (추가 공간 없이 원본 리스트 수정)

***

### :white\_check\_mark: Java 코드

```java
public class Solution {
    public ListNode deleteDuplicates(ListNode head) {
        if (head == null) return null;
        
        ListNode curr = head; // 현재 노드 포인터
        
        while(curr.next != null) { // 리스트 순회
            if(curr.val == curr.next.val) {
                curr.next = curr.next.next; // 중복이면 건너뛰기
            } else {
                curr = curr.next; // 중복이 아니면 다음 노드로 이동
            }
        }
        return head; // 중복 제거된 리스트 반환
    }
    
    //테스트용 메서드 (배열 => LinkedList 변환)
    public static ListNode createLinkedList(int[] values) {
        if(values.length == 0) return null;
        ListNode head = new ListNode(values[0]);
        ListNode curr = head;
        for(int i = 1; i < values.length; i++) {
            curr.next = new ListNode(values[i]);
            curr = curr.next;
        }
        return head;
    }
    
    //출력 메서드
    public static void printLinkedList(ListNode head) {
        ListNode curr = head;
        while(curr != null) {
            System.out.println(curr.val + " ");
            curr = curr.next;
        }
        System.out.println();
    }
    
    public static void main(String[] args) {
        Soultion solution = new Solution();
        
        ListNode head1 = createLinkedList(new int[]{1,1,2});
        ListNode result1 = solution.deleteDuplicates(head1);
        printLinkedList(result1); // result : 1 2
        
        ListNode head2 = createLinkedList(new int[]{1,1,2,3,3});
        ListNode result2 = solution.deleteDuplicates(head2);
        printLinkedList(result2); // result : 1 2 3
    }
}
```

#### :pushpin:코드 설명

1. `deleteDuplicates(ListNode head)`
   1. 현재 노드(`curr` ) 를 따라가면서 **중복이 있는 경우, curr.next 를 건너뛰어 삭제.**
   2. 리스트 끝까지 반복하면서 중복을 제거
2. `createLinkedList(int[] values)`&#x20;
   1. 배열을 **연결 리스트로 변환하는 메서드**
3. `printLinkedList(ListNode head)`&#x20;
   1. 연결 리스트를 출력하는 메섣,
