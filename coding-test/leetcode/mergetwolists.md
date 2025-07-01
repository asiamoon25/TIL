# MergeTwoLists

```java
public static ListNode mergeTwoLists(ListNode list1, ListNode list2) {    
        if (list1 == null) return list2;
        if (list2 == null) return list1;
        
        List<Integer> list1Array = toListFromListNode(list1);
        List<Integer> list2Array = toListFromListNode(list2);
    
        List<Integer> mergedList = new ArrayList<>();

        mergedList.addAll(list1Array);
        mergedList.addAll(list2Array);
        Collections.sort(mergedList);

       
        ListNode result = toListNodeFromList(mergedList);

        return result;
    }

    public static List<Integer> toListFromListNode(ListNode node) {
        List<Integer> listArr = new ArrayList<>();
        
        if (node == null) {
            return listArr;
        }
        
        int count = nodeSize(node);
        ListNode current = node.next;
        for(int i = 0; i < count; i++) {
            if(i == 0) {
                listArr.add(node.val);
            } else {
                if(current == null) break;
                listArr.add(current.val);
                current = current.next;
            }
        }

        return listArr;
    }

    public static int nodeSize(ListNode node) {
        int count = 0;
        ListNode current = node;

        while (current != null) {
            count++;
            current = current.next;
        }

        return count;
    }

    public static ListNode toListNodeFromList(List<Integer> list) {

        int count = list.size();

        if(list == null || list.isEmpty()) return new ListNode();
        ListNode node = new ListNode(list.get(0));

        ListNode current = node;
        
        for(int i = 1; i < count; i++) {
            if(current == null) break;
            current.next = new ListNode(list.get(i));
            current = current.next;
        }

        return node;
    }
```

<figure><img src="../../.gitbook/assets/image (36).png" alt=""><figcaption></figcaption></figure>

***

리팩토링 후



