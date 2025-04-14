# Permutation, Combination, Subset

**조합론(Combinatorics)** 알고리즘 세 가지인 **Permutation(순열), Combination(조합), Subset(부분집합)** 임.

이 세 가지는 **완전탐색, 백틀래킹, DFS 문제** 에 자주 활용되고, 경우의 수 계산 뿐만 아니라 **실제로 탐색 알고리즘 구현** 할 때도 유용함.

***

### 1. 개념 요약

| 용어                                          | 설명                       | 예시 (`[1, 2, 3]`)                  | 순서 고려     |
| ------------------------------------------- | ------------------------ | --------------------------------- | --------- |
| <p><strong>Permutation</strong><br>(순열)</p> | n개 중 r개를 **순서 있게 선택**    | `[1,2]`, `[2,1]`, `[1,3]`...      | ✅ Yes     |
| <p><strong>Combination</strong><br>(조합)</p> | n개 중 r개를 **순서 없이 선택**    | `[1,2]`, `[1,3]`, `[2,3]`         | ❌ No      |
| <p><strong>Subset</strong><br>(부분집합)</p>    | n개 원소의 **모든 조합(포함/비포함)** | `[]`, `[1]`, `[2,3]`, `[1,2,3]` 등 | ❌ No (집합) |

***

### Permutation(순열)

#### 개념

* 순서 중요
* $$nPr = \frac {n!}{(n-r)!}$$

#### 예시

```java
void permute(int[] arr, boolean[] visited, List<Integer> path) {
    if(path.size() == r) { // r 은 전역 변수
        System.out.println(path);
        return;
    }
    for(int i = 0; i < arr.length; i++) {
        if(!visited[i]) {
            visited[i] = true;
            path.add(arr[i]);
            permute(arr, visited, path);
            path.remove(path.size() - 1);
            visited[i] = false;
        }
    }
}
```

* 호출 : `permute(arr, new boolean[arr.length], new ArrayList<>());`&#x20;

***

### Combination(조합)

#### 개념

* 순서 중요하지 않음.
* $$nCr = \frac{n!}{r!(n - r)!)}$$

#### 예시

<pre class="language-java"><code class="lang-java"><strong>void combine(int[] arr, int start, List&#x3C;Integer> path) {
</strong>    if(path.size() == r) { // r 은 전역 변수
        System.out.println(path);
        return;
    }
    for(int i = start; i &#x3C; arr.length; i++) {
        path.add(arr[i]);
        combine(arr, i + 1, path);
        path.remove(path.size() - 1);
    }
}
</code></pre>

* 호출 : `combine(arr, 0, new ArrayList<>());`&#x20;

***

### Subset(부분집합)

#### 개념

* 포함/ 비포함의 모든 경우
* 원소 n 개 ⇒ 부분집합 개수 $$2^n$$

#### 예시 1 : 재귀

```java
void subset(int[] arr, int idx, List<Integer> path) {
    if (idx == arr.length) {
        System.out.println(path);
        return;
    }
    
    // 현재 원소 포함
    path.add(arr[idx]);
    subset(arr, idx + 1, path);
    path.remove(path.size() - 1);
    
    // 현재 원소 미포함
    subset(arr, idx + 1, path);
}
```

* 호출 : `subset(arr, 0, new ArrayList<>());` &#x20;

#### 예시 2 : 비트마스크

```java
for(int i = 0; i < ( 1 << n ); i++) {
    List<Integer> subset = new ArrayList<>();
    for(int j = 0; j < n; j++) {
        if((i & (1 << j)) > 0) {
            subset.add(arr[j]);
        }
    }
    System.out.println(subset);
}
```

***

### 차이점 정리

| 항목   | Permutation        | Combination                        | Subset   |
| ---- | ------------------ | ---------------------------------- | -------- |
| 순서   | 중요함                | 상관 없음                              | 없음       |
| 중복   | 안됨 (기본)            | 안됨 (기본)                            | 일부 포함됨   |
| 길이   | 고정 r개              | 고정 r개                              | 0 \~ n   |
| 출력 예 | `[1, 2]`, `[2, 1]` | `[1, 2]`, `[2, 1]`은 중복 → `[1, 2]`만 | 모든 조합 가능 |

***

### 시간복잡도

| 항목          | 시간복잡도           |
| ----------- | --------------- |
| Permutation | O(n!)           |
| Combination | O(2ⁿ) 또는 O(nCr) |
| Subset      | O(2ⁿ)           |

> 따라서 원소 개수가 많아지면 순열은 조합보다, 조합은 부분집합보다 훨씬 빠르게 폭발함. ( 시간복잡도가 너무 빨리 커짐)
>
> **실전 코딩테스트에서는 보통 n <= 10 \~ 15 수준에서만 완전탐색 적용 가능**

***

### 4. 활용 예시

| 알고리즘 유형 | 활용                           |
| ------- | ---------------------------- |
| 순열      | 경로 탐색 (여행 경로, 외판원 문제), 자리 배치 |
| 조합      | 부분합 문제, 팀 구성, 비밀번호 조합        |
| 부분집합    | 최대/최소 합, 특정 조건 만족 조합 찾기      |

