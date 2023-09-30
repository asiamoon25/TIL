---
description: 람다표현식
---

# Lambda Expression

### 람다?

JAVA 8에 추가된 표현식

람다 표현식은 매개변수를 받아 값을 반환하는 짧은 코드 블록

메서드와 유사하지만 이름이 필요하지 않으며 메서드 본문에서 바로 구현 가능



간단한 람다 표현식에는 단일 매개변수와 표현식이 포함



```
parameter -> expression
```

2개 이상의 변수 사용 시

```
(parameter1, parameter2) -> expression
```

제한적인 부분이 있음.

즉시 값을 반환해야하며, 변수 할당 또는 if 와 for 같은 것을 포함할 수 없음.

더 복잡한 작업을 하려면 중괄호화 함께 코드 블록을 사용할 수 있다. 람다식이 값을 반환해야하는 경우 코드 블록에 return 이 있어야한다.

```
(parameter1, parameter2) -> { code block }
```



### 사용법

ArrayList 안에 있는   forEach 문으로 List의 각 요소들을 print 하는 코드이다.

```java
import java.util.ArrayList;

public class Main {
  public static void main(String[] args) {
    ArrayList<Integer> numbers = new ArrayList<Integer>();
    numbers.add(5);
    numbers.add(9);
    numbers.add(8);
    numbers.add(1);
    numbers.forEach( (n) -> { System.out.println(n); } );
  }
}
```
