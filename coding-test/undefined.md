# 대비

### Java 로 코테 준비 시 체크리스트

#### 자주 쓰이는 Java 문법/클래스

<table><thead><tr><th>기능</th><th>클래스/메서드 예시</th><th data-hidden></th></tr></thead><tbody><tr><td>정렬</td><td><code>Array.sort()</code> , <code>Collections.sort()</code> </td><td></td></tr><tr><td>해시</td><td><code>HashMap</code> ,<code>HashSet</code> , <code>LinkedHashMap</code></td><td></td></tr><tr><td>우선순위 큐</td><td><code>PriorityQueue</code> </td><td></td></tr><tr><td>스택/큐</td><td><code>Stack</code> , <code>ArrayDeque</code> </td><td></td></tr><tr><td>배열 처리</td><td><code>int[]</code> , <code>List&#x3C;Integer></code> 등</td><td></td></tr><tr><td>문자열</td><td><code>StringBuilder</code> , <code>split()</code> , <code>substring()</code> </td><td></td></tr><tr><td>시간복잡도 개선</td><td><code>prefix sum</code> , <code>sliding window</code> 직접 구현</td><td></td></tr></tbody></table>

### Java 에서 주의할 점

* 배열은 **기본형 타입** ( `int[]` ) 위주로 사용 ⇒ 속도 빠름
* `List` 와 `ArrayList` 구분 명확히
* `String` 연결은 반드시 `StringBuilder` 써야 시간 초과 안남.
* `for-each` vs `indexed for-loop` 사용 맥락 구분

***

### 예시 문제

#### 최소 이동 거리(Sliding Window + Greedy)

길이 N짜리 배열 A가 주어질 때, 연속된 부분합이 S 이상이 되는 가장 짧은 구간의 길이를 구하라. 없으면 0 반환

```java
A = [1, 2, 3, 4, 5], S = 11
```

**출력 예시**

```cpp
3 // 부분합 [3,4,5]
```

**힌트**

* `two-pointer` 방식 활용
* 시간복잡도: `O(N)`&#x20;



**연습 방향**

| 목표            | 할 일                                |
| ------------- | ---------------------------------- |
| Java 최적 코드 습관 | `Codility 연습 후` 코드 리뷰하면서 리팩토링      |
| 자주 나오는 유형 공략  | Codility Lessons + 위 유형별 실습        |
| 실전 감각         | 30분 타이머 두고 Java로 문제 직접 풀어보기        |
| 디버깅 연습        | `System.out.println` 적극 활용해서 로직 검증 |

