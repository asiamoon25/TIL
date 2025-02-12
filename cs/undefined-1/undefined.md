# 바빌로니아 법

바빌로니아 법은 **제곱근(Squre Root)** 을 근사적으로 구하는 방법 중 하나로, 뉴턴-랩슨 방법과 유사한 알고리즘

***

### 개념

임의의 양수 `S` 에 대해 제곱근 $$\sqrt S$$ 을 구하려면 아래의 점화식을 이용하여 반복 계산 하면 됨.

$$
x_{n+1} = \frac{1}{2}(x_n + \frac{S}{x_n})
$$



* $$x_n$$ 은 현재 추정값
* $$x_{n+1}$$ 은 새로운 추정값&#x20;
* 반복적으로 계산하면 점점 실제 제곱근에 가까워짐
* 초기값 $$x_0$$ 은 적당한 양수로 설정 (보통 $$S$$ 또는 $$\frac{S}{2}$$)

***

### Python Code

```python
def sqrt_babylonian(S, tolerance=1e-10):
    if S < 0:
        raise ValueError("음수의 제곱근은 구할 수 없음")
        
    x = S 
    while True:
        new_x = 0.5 * (x+S / x) #babylonian
        if abs(new_x - x) < tolerance: # 오차 허용 범위 내이면 종료
            break
        x = new_x
    return new_x
```

***

### Java Code

```java
public class BabylonianSqrt {
    public static double sqrt(double S, double tolerance) {
        if (S < 0) {
            throw new IllegalArgumentException("음수의 제곱근은 계산할 수 없음");
        }
        
        double x = S; // 초기 추정값 설정
        double newX;
        
        while(true) {
            newX = 0.5 * (x + S / x); // 바빌로니아 법
            
            if(Math.abs(newX - x) < tolerance) { // 오차가 허용 범위 내이면 종료
                break;
            }
        }
    }
    public static void main(String[] args) {
        System.out.println(sqrt(25, 1e-10)); // 5.0
        System.out.println(sqrt(2, 1e-10)); // 약 1.41421356
        System.out.println(sqrt(10, 1e-10)); // 약 3.16227766
    }
}
```

* `sqrt(double S, double tolerance)`: 바빌로니아 법을 사용해 $$\sqrt{S}$$ 계산
  * $$x_{n+1} = \frac{1}{2}(x_n + \frac{S}{x_n})$$ 식을 무한 반복
  * 오차(`tolerance`) 가 충분히 작아질 때까지 반복

