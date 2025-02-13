# Climbing Stairs

{% embed url="https://leetcode.com/problems/climbing-stairs" %}

#### 한국어

당신은 계단을 오르고 있습니다. 정상에 도달하려면 nnn 계단을 올라야 합니다.

한 번에 1계단 또는 2계단씩 오를 수 있을 때, 정상까지 도달하는 서로 다른 방법의 수는 몇 가지인가요?



### 1. 문제 분석

* 계단을 `n` 개의 계단을 올라야함.
* 한 번에 **1계단 또는 2계단** 오를 수 있음.
* 정상까지 도달하는 **서로 다른 방법의 수** 를 구해야 함.

### 2. 작은 경우부터 생각해 보기(패턴 찾기)

계단을 오르는 방법을 작은 숫자로 직접 세어보면 규칙이 보임.



예제 1: `n = 1`&#x20;

* 방법: (1)
  * ⇒ 1가지 방법

예제 2: `n = 2`&#x20;

* 방법: (1 + 1), (2)
  * ⇒ **2가지 방법**

예제 3: `n = 3`&#x20;

* 방법: **(1+1+1), (1+2), (2+1)**\
  → **3가지** 방법

#### **예제 4: `n = 4`**

* 방법:
  1. `(1+1+1+1)`
  2. `(1+1+2)`
  3. `(1+2+1)`
  4. `(2+1+1)`
  5. `(2+2)`\
     → **5가지** 방법

### 3. 점화식(재귀식) 찾기

각 숫자의 결과를 보면 일정한 패턴이 보임.

| `n` | 방법의 수 |
| --- | ----- |
| 1   | 1     |
| 2   | 2     |
| 3   | 3     |
| 4   | 5     |
| 5   | 8     |
| 6   | 13    |

**패턴 발경** :&#x20;

* `f(3) = f(2) + f(1) = 2 + 1 = 3`
* `f(4) = f(3) + f(2) = 3 + 2 = 5`
* `f(5) = f(4) + f(3) = 5 + 3 = 8`

⇒ 즉, 계단 `n` 개를 오르는 방법의 수는 다음과 같은 점화식을 따름

$$f(n) = f(n-1) + f(n-2)$$

* `f(n-1)` : 마지막에 `1 계단` 을 올랐을 때의 경우의 수
* `f(n-2)` : 마지막에 `2 계단` 을 올랐을 때의 경우의 수

**이 점화식은 피보나치 수열 과 동일한 구조임**

(단, 초기값 `f(1) = 1`, `f(2) = 2` )



### 4. 왜 재귀로 풀 수 있는가?

* $$f(n) = f(n-1) + f(n-2)$$ 이므로, `n` 을 구하려면 먼저 `n-1` 과 `n-2` 를 계산해야함.
* $$f(n-1)$$ 을 구하려면 다시 $$f(n-2)$$ 와 $$f(n-3)$$ 이 필요하고, 계속 쪼개지게 됨.
* 따라서, 문제를 작은 문제 (`n-1` , `n-2` 등)로 쪼개서 해결하는 "**분할 정복(Divide & Conquer)**" 형태가 됨.

#### 재귀 호출 구조 예시(n = 5 일 때)

```scss
climbStairs(5)
├── climbStairs(4)
│   ├── climbStairs(3)
│   │   ├── climbStairs(2) → 2
│   │   ├── climbStairs(1) → 1
│   │   └── 결과: 3
│   ├── climbStairs(2) → 2
│   └── 결과: 5
├── climbStairs(3)
│   ├── climbStairs(2) → 2
│   ├── climbStairs(1) → 1
│   └── 결과: 3
└── 결과: 8
```

***

### 5. 재귀 코드

```java
public class SlimbingStairs{
    public static int climbStairs(int n) {
        if (n == 1) return 1;
        if (n == 2) return 2;
        return climbStairs(n-1) + climbStairs(n - 2);
    }
    
    public static void main(String[] args) {
        System.out.println(climbStairs(5)); // result : 8
    }
}
```

***

### 6. 더 빠른 방법(DP)

반복문으로 `O(n), O(1)` 최적화

```java
public class ClimbingStairs {
    public static int climbStairs(int n) {
        if (n == 1) return 1;
        if (n == 2) return 2;
        
        int first = 1;
        int sencond = 2;
        for (int i = 3; i <= n; i++) {
            int third = first + second;
            first = second;
            second = third;
        }
        return second;
    }
    
    public static void main(String[] args) {
        System.out.println(climbStairs(5)); // result : 8
    }
}
```
