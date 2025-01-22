# 캡슐화

## 1. 캡슐화란?

**캡슐화** 는 **객체 지향 프로그래밍** 의 핵심 개념중 하나로, **객체의 데이터와 동작(메서드) 을 하나의 단위로 묶고 외부에서의 직접 접근을 제한** 하는 것

### 목적

#### 1. 데이터 보호

* 외부에서 객체의 내부 데이터에 직접 접근하지 못하도록 보호

#### 2. 정보 은닉

* 필요한 정보만 외부에 공개하고, 불필요한 정보는 숨김.

#### 3. 코드 유지보수성 향상

* 데이터를 변경하거나 로직을 수정해 외부 코드에 영향을 최소화.

#### 4. 객체의 일관성 유지

* 데이터의 유효성을 보장

***

## 2. Java 에서 캡슐화 구현 방법

### 1. 접근 제한자 사용

Java 에서 캡슐화를 구현하기 위해 **접근 제한자(access modifier)** 를 사용함.

| `private` | O | X | X | X |
| --------- | - | - | - | - |

| `default` | O | O | X | X |
| --------- | - | - | - | - |

| `protected` | O | O | O | X |
| ----------- | - | - | - | - |

| `public` | O | O | O | O |
| -------- | - | - | - | - |

### 2. 캡슐화 구조

1. **필드(데이터)** 를 `private` 으로 선언하여 외부 접근 차단.
2. 필드에 접근하거나 수정할 때 **Getter** 와 **Setter** 메서드 (`public` ) 를 통해 안전하게 접근.

**예제 : 캡슐화된 클래스**

```java
public class Person{
    // field : private 로 선언하여 외부 접근 차단.
    private String name;
    private ing age;
    
    // constructor
    public Person(String name, int age){
        this.name = name;
        if (age < 0) {
            throw new IllegalArgumentException("Age cannot be negative");
        }
        this.age = age;
    }
    
    // Getter:  read data
    public String getName(){
        return name;
    }
    
    public int getAge() {
        return age;
    }
    
    // Setter : 데이터를 변경하는 메서드
    public void setName(String name) {
        this.name = name;
    }
    
    public void setAge(int age){
        if(age < 0){
            throw new IllegalArgumentException("Age cannot be negative");
        }
        this.age = age;
    }
}
```

***

## 3. 캡슐화의 장점&#x20;

### 1. 데이터의 보호

* 잘못된 데이터의 직접 수정 방지
* 예 :  나이 필드에 음수 값이 들어가 않도록 Setter 에서 유효성 검사.

### 2. 정보 은닉

* 외부에 불필요한 정보를 숨기고, 필요한 정보만 공개

### 3. 코드 유연성

* 필드 이름이나 로직을 변경해도 Getter 와 Setter 를 통해 외부 코드와의 호환성을 유지

### 4. 객체의 일관성 보장

* 유효성 검사를 통해 잘못된 상태로 객체가 생성되거나 변경되는 것을 방지.

