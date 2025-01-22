# Record

## 1. Record 란?

`Record` 는 단순히 데이터를 담기 위한 클래스를 간단히 정의할 수 있도록 도입된 새로운 구조

기존의 보일러플레이트 코드(생성자, getter, equals, hashCode, toString 등) 를 자동으로 생성해 주는 데이터 중심 클래스임.



***

## 2. Record 의 특징

#### 1. 간결성

* 데이터를 저장하기 위해 필요한 생성자, `getter`, `equals`, `hashCode`, `toString` 메서드가 자동으로 생성됨.
* 기존의 **Java Bean** 스타일과 달리 보일러플레이트 코드를 작성할 필요가 없음.

#### 2. 불변성

* Record 클래스의 필드는 기본적으로 `final` 로 선언됨.
* **setter 메서드가 없으며**, 값 변경이 불가능함.

#### 3. 목적

* 주로 데이터를 전달하거나 저장힉 위한 용도로 설계되었음.
* 복잡한 비즈니스 로직을 포함하기엔 적합하지 않음.

#### 4. 클래스 상속 불가

* **Record** 는 암묵적으로 `final` 로 선언되므로 상속할 수 없음.

***

## 3. Record 사용법

### 기본 구조

Record 클래스를 정의하려면 `record` 키워드를 사용함.

```java
public record Person(String name, int age){}
```

### 2. Record 인스턴스 생성

```java
Person person = new Person("Alice", 25);
System.out.println(person.name()); // Alice
System.out.println(person.age()); // 25
```

### 3. 자동 생성 메서드

* **Getter** : 필드명을 메서드로 사용해 값을 반환
* `toString()`: 필드 이름과 값을 포함한 문자열 생성
* `equals()`: 두 객체가 동일한 값을 가지면 `true`
* `hashCode()`: 객체를 고유하게 식별하는 해시코드

```java
Person person1 = new Person("Alice", 25);
Person person2 = new Person("Alice", 25);

System.out.println(person1); // Person[name=Alice, age=25]
System.out.println(person1.equals(person2)); // true
System.out.println(person1.hashCode()); // 동일한 해시코드 반환
```

***

## 4. Record 의 추가 기능

### 1. 커스텀 메서드

`Record` 에도 커스텀 메서드를 추가할 수 있음.

```java
public record Person(String name, int age){
    public String greeting() {
        return "Hello, my name is " + name + " and I am " + age + " years old.";
    }
}
```

```java
Person person = new Person("Alice", 25);
System.out.println(person.greeting()); // Hello, my name is Alice and I am 25 years old.
```



### 2. 생성자 오버라이딩&#x20;

* Record 는 압축된 **Canonical Constructor** 를 자동으로 생성함.
* 생성자를 명시적으로 정의하거나, 입력값을 검증할 수 있음.

```java
public record Person(String name, int age){
    public Person{
        if(age < 0) {
            throw new IllegalArgumentException("Age must be non-negative");
        }
    }
}
```

***

## 5. Record 의 제약 사항

### 1. 상속 불가

* Record 는 암묵적으로 `final` 로 선언되므로 상속할 수 없음.
* 대신 인터페이스를 구현할 수 있음.

```java
public record Person(String name, int age) implements Comparable<Person> {
    @Override
    public int compareTo(Person other){
        return this.name.comapreTo(other.name);
    }
}
```

### 2. 가변 필드 사용 불가

* 필드는 반드시 `final` 이어야 하며, 가변 데이터를 포함할 수 없음.
* 배열 같은 경우, 필드가 `final` 이지만 내부 값은 변경 가능함.

```java
public record DataHolder(int[] data){}

DataHolder holder = new DataHolder(new int[]{1,2,3});
holder.data()[0] = 42; // 내부 데이터 변경 가능
```

### 3. 복잡한 로직 포함 불가

* Record 는 간단한 데이터 저장용도로 설계되었으므로 복잡한 비즈니스 로직에는 적합하지 않음.

