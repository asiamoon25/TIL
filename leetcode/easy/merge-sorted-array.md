# Merge Sorted Array

{% embed url="https://leetcode.com/problems/merge-sorted-array" %}

<figure><img src="../../.gitbook/assets/image (2).png" alt=""><figcaption></figcaption></figure>

개느림...

```java
public static void merge(int[] nums1, int m, int[] nums2, int n) {
    for(int i = m; i < m + n; i++) {
        nums1[i] = nums2[i - m];
    }

    Arrays.sort(nums1);
}
```

***

### :pushpin:코드 속도가 낮은 이유

현재 코드에서 **속도가 낮은 이유** 는 `Arrays.sort(nums1)` 때문임.

```java
for(int i = m; i < m + n; i++) {
    nums1[i] = nums2[i - m]; // nums2의 요소를 nums1의 뒤쪽에 복사
}
Arrays.sort(nums1); // 전체 배열을 다시 정렬
```

이 코드에서 `Arrays.sort(nums1)` 는 최악의 경우 $$O((m+n) log(m + n))$$ 의 시간 복잡도를 가짐.

즉, **기존의 정렬된 배열을 활용하지 않고, 강제로 다시 정렬하는 방식** 이라 비효율 적임.

***

### :white\_check\_mark: 개선 방법: 병합 정렬 (Two Pointers) 활용

이 문제는 **두 개의 정렬된 배열을 병합하는 과정** 이므로, 이미 정렬된 두 배열을 활용해 **뒤에서부터 병합하는 방식(역순 병합)** 을 사용하면 효율적임.

:hourglass:**시간 복잡도** : $$O(m + n)$$ (정렬 없이 병합만 수행)

:pushpin:**공간 복잡도**: $$O(1)$$ (추가 공간 없이 `nums1` 내부에서 직접 병합)

***

### :rocket: 개선된 코드(Two Pointers, 역순 병합)

```java
import java.util.Arrays;

public class MergeSortedArray{
    public static void merge(int[] nums1, int m, int[] nums2, int n) {
        int p1 = m - 1; // nums1 의 마지막 실제 요소 위치
        int p2 = n - 1; // nums2 의 마지막 요소 위치
        int p = m + n - 1; // 병합 후 nums1의 마지막 위치
        
        while(p1 >= 0 && p2 >= 0) {
            if(nums1[p1] > nums2[p2]) {
                nums1[p] = nums1[p1];
                p1--;
            } else {
                nums1[p] = nums2[p2];
                p2--;
            }
            p--;
        }
        
        // nums2 의 남은 요소 처리 (nums1은 이미 정렬되어 있으므로 따로 처리할 필요는 없음.)
        while(p2 >= 0) {
            nums1[p] = nums2[p2];
            p2--;
            p--;
        }
    }
    // 테스트 실행
    public static void main(String[] args) {
       int[] nums1 = {1, 2, 3, 0, 0, 0};
       int m = 3;
       int[] nums2 = {2, 5, 6};
       int n = 3;

       merge(nums1, m, nums2, n);
       System.out.println(Arrays.toString(nums1)); // 출력: [1, 2, 2, 3, 5, 6] 
    }
}
```

:pushpin: **코드 설명**

1. **포인터 3개 설정**
   1. `p1 = m - 1` : `nums1` 의 **마지막 원소 위치 (유효 데이터 범위)**
   2. `p2 = n - 1` : `nums2` 의 **마지막 원소 위치**
   3. `p = m + n - 1` : `nums1` 의 **마지막 빈 공간부터 채우기 시작**
2. \*\*뒤에서부터 큰 값을 선택하여 배치($$O(m+n)$$)
   1. `nums1[p1]` 과 `nums2[p2]` 를 비교
   2. 큰 값을 `nums1[p]` 에 배치 후 해당 포인터 감소
   3. `p1` 또는 `p2` 중 하나가 끝날 때까지 반복
3. 남은 `nums2` **요소 처리**
   1. `nums2` 의 남은 요소가 있다면 `nums1` 앞부분에 복사

***

### :pushpin: 왜 더 빠른가?

* 정렬 ($$O((m+n) log(m+n))$$) 을 제거하고 `O(m+n)` 으로 최적화
* **한 번의 반복만 수행하며 추가 공간(O(1)) 없이 해결**
* 이미 정렬된 배열을 활용하여 효율적 정렬 유지

***

### :rocket:성능차이

| 코드 방식                   | 시간 복잡도              | 공간 복잡도 | 특징                 |
| ----------------------- | ------------------- | ------ | ------------------ |
| 기존 코드 (`Arrays.sort()`) | `O((m+n) log(m+n))` | `O(1)` | 정렬을 무조건 수행하므로 비효율적 |
| 개선 코드 (Two Pointers)    | `O(m + n)`          | `O(1)` | 직접 병합하여 불필요한 정렬 제거 |

👉 **배열이 정렬된 상태이므로, 불필요한 `sort()`를 없애고 뒤에서부터 병합하면 성능이 향상됨!** 🚀
