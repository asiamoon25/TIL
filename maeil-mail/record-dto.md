# Record를 DTO 로 사용하는 이유

## Record 란?

<mark style="background-color:blue;">Record</mark> 는 Java 16 에서 정식 출시된 특별한 유형의 클래스로 **불변성(Immutable)** 을 기본으로함.



기존의 클래스와 달리 모든 필드가 `final` 키워드로 선언되며, 객체 생성 후 변경할 수 없음.

또한 필드 선언만으로 자동으로 생성자, `getter`, `equals()`, `hashCode()`, `toString()` 등 메서드를 자동으로 생성해 주어 <mark style="background-color:blue;">보일러 플레이트</mark> 코드를 줄일 수 있음. 이러한 특성으로 인해 멀티 스레드 환경에서 데이터가 의도치 않게 변경되지 않고 안전하게 전달할 수 있음.



```java
// 기존 클래스 기반 DTO
public class MemberDto{
    private final String name;
    private final String email;
    private final int age;
    
    public MemberDto(String name, String email, int age) {
        this.name = name;
        this.email = email;
        this.age = age;
    }
    
    public String getName() {
        return name;
    }
    
    public String getEmail(){
        return email;
    }
    
    public int getAge() {
        return age;
    }
}

// Record. 생성자, getter, hashCode(), equals(), toString() 자동 완성
public record MemberDto(String name, String email, int age){}
```

## Record 로 생성한 모든 객체는 DTO?

모든 Record 객체가 DTO 인 것은 아님. Record  는 단순히 데이터를 <mark style="background-color:blue;">캡슐화</mark> 하는 역할을 하는데, DTO 외에도 값 객체(Value Object) 등의 다양한 용도로 사용될 수 있음&#x20;

```java
// value object 로 사용
public record Coordinates(double x, double y) {}
```



DTO 는 계층 간 데이터 전송을 목적으로 하느 객체이 반면, <mark style="background-color:blue;">VO</mark> 는 도메인 모델 내에서 특정 값을 표현하는 객체로 사용됨. 따라서, Record는 이 두가지 모드에 적합하게 사용할 수 있지만, 그 목적에 따라 사용 방법이 달라짐.

## Record 와 VO 비교

Record 와 VO 는 모두 객체의 상태가 변경되지 않는 것을 보장함. 또 데이터를 캡슐화 하여 표현하는데 초점을 맞춤.&#x20;

마지막으로 <mark style="background-color:orange;">VO 는 값 기반의 동등성을 가지며, Record 도 동일한 필드 값을 가지면 동일한 객체로 간주된다</mark>는 점이 공통점.

VO 는 도메인 모델 내에서 특정 개념을 표현하고, 도메인 로직과 밀접하게 관련이 있음.&#x20;

즉, VO 는 비즈니스 로직이나 규칙을 가질 수 있음. 반면에 Record 는 단순히 데이터를 캡슐화하여 전달하는데 의미가 있음.



결론적으로 Record 는 VO를 구현하는데 적합하지만, VO의 모든 특성을 완벽히 대체하지는 않음. VO 는 더 넓은 도메인 맥락에서 사용되며, 비즈니스 로직을 포함할 수 있음.



## 한계

* 다른 클래스 상속 안됨.(extends)
* 필드가 final 로 선언되기 때문에 확장이 어려움
* 비즈니스 로직을 포함하기에 적절하지 않음.
* Java 14 도는 Java 16 버전에서 호환이 불가능한점.
