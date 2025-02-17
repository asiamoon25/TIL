# 변수

### Java에서 변수(Variable)란?

변수는 **값을 저장할 수 있는 메모리 공간** 으로, Java 프로그램에서 데이터를 저장하고 조작하는 데 사용됨.

변수를 선언하면 특정 데이터 타입을 지정하여 해당 변수에 저장할 수 있는 값의 종류를 결정함.

***

### 1. 변수의 선언과 초기화

변수를 사용하려면  먼저 **선언(declaration)** 한 후, **초기화(Initialization)** 해야함.

#### 변수 선언

```java
int number; // 정수형 변수 선언
String name; //문자열 변수 선언
```

* `int` : 변수 타입(정수)
* `number` : 변수 이름

#### 변수 초기화

```java
number = 10; // 변수에 값 할당
name = "Java"; // 문자열 값 할당
```

**선언과 동시에 초기화** 할 수 있음.

```java
int age = 25;
double pi = 3.14;
boolean isJavaFun = true;
```

***

### 2. Java 변수의 종류&#x20;

Java 에서는 변수를 **저장되는 위치와 생명 주기(lifetime)** 에 따라 다음과 같이 분류함.

#### 1) 지역 변수(Local Variable)

* 특정 **메서드 또는 블록 내에서만** 사용 가능
* 메서드가 끝나면 메모리에서 자동 제거됨.
* **반드시 초기화 후 사용해야함**

```java
public class Main{
    public static void main(String[] args) {
        int localVar = 10; // 지역 변수
        System.out.println(localVar); // 출력: 10
    }
}
```

#### 2) 인스턴스 변수(Instance Variable)

* **클래스 내부에 선언되고, 객체(instance)마다 별도로 저장됨**
* 객체가 생성될 때 메모리에 할당되고, 객체가 삭제될 때 해제됨.
* **초기화하지 않으면 기본값(default value)이 설정됨**
  * `int` ⇒ `0`&#x20;
  * `double` ⇒ `0.0`&#x20;
  * `boolean` ⇒ `false`&#x20;
  * `String` ⇒ `null`&#x20;

```java
class Person{
    String name; // 인스턴스 변수(기본값 : null)
    int age; // 인스턴스 변수(기본값 : 0)
}

public class Main {
    public static void main(String[] args) {
        Person p1 = new Person();
        System.out.println(p1.name); // result : null
        System.out.println(p1.age); // result : 0
    }
}
```

#### 3) 클래스 변수(Class Variable, static 변수)

* `static` 키워드를 사용하여 선언된 변수
* **클래스 로딩 시 한 번만 생성** 되며, 모든 객체가 공유
* 프로그램이 종룔될 때까지 유지됨.

```java
class Counter {
    static int count = 0; // class variable
    
    Counter() {
        count++; // 모든 객체가 같은 변수를 공유
    }
}

public class Main {
    public static void main(String[] args) {
        new Counter();
        new Counter();
        System.out.println(Counter.count); // result : 2
    }
}
```

***

### 3. Java 변수의 데이터 타입

변수는 저장할 데이터 종류에 따라 여러 **데이터 타입** 으로 선언할 수 있음.

#### 1) 기본형(Primitive Type)

| `byte` | 1 byte | `0` | -128 \~ 127 범위의 정수 |
| ------ | ------ | --- | ------------------ |

| `short` | 2 byte | `0` | -32,768 \~ 32,767 범위의 정수 |
| ------- | ------ | --- | ------------------------ |

| `int` | 4 byte | `0` | 일반적인 정수형 |
| ----- | ------ | --- | -------- |

| `long` | 8 byte | `0L` | 큰 범위의 정수 |
| ------ | ------ | ---- | -------- |

| `float` | 4 byte | `0.0f` | 실수 (소수점 표현) |
| ------- | ------ | ------ | ----------- |

| `double` | 8 byte | `0.0d` | 더 정밀한 실수 표현 |
| -------- | ------ | ------ | ----------- |

| `char` | 2 byte | `\u0000` | 한 글자 문자 (Unicode 지원) |
| ------ | ------ | -------- | -------------------- |

| `boolean` | 1 bit | `false` | 논리값 (`true` 또는 `false`) |
| --------- | ----- | ------- | ----------------------- |

```java
int a = 100;
double b = 3.14;
boolean isJavaGood = true;
char grade = 'A';
```

#### 2) 참조형(Reference Type)

* **객체의 메모리 주소를 저장하는 변수**
* 기본형과 달리 `null` 값을 가질 수 있음.
* 배열, 클래스, 인터페이스, 컬렉션 등이 포함됨.

```java
String text = "Hello, Java!";
int[] numbers = {1,2,3,4,5};
```

***

### 4. 변수의 유효 범위(Scope)

변수는 선언된 위치에 따라 **유효 범위(Space)** 가 결정됨.

| 변수 종류   | 선언 위치         | 접근 가능 범위         |
| ------- | ------------- | ---------------- |
| 지역 변수   | 메서드, 블록 내부    | 해당 블록 내에서만 사용 가능 |
| 인스턴스 변수 | 클래스 내부(메서드 밖) | 객체 생성 후 사용 가능    |
| 클래스 변수  | static 키워드 사용 | 모든 객체가 공유        |

```java
class Example {
    static int staticVar = 10; // class variable
    int instaceVar = 20; // instance variable
    
    void method() {
        int localVar = 30; // local variable
        System.out.println(localVar); // 가능
        System.out.println(instanceVar); // 가능
        System.out.println(staticVar); // 가능
    }
}       
```

***

### 5. 상수(Constant) 와 final 키워드

변수의 값을 변경할 수 없게 만들려면 `final` 키워드를 사용

```java
final int MAX_VALUE = 100;
// MAX_VALUE = 200; // error! 변경 불가능
```

* `final` 변수는 한 번 할당되면 변경할 수 없음
* **일반적으로 대문자로 표기**

***

### 6. 변수 타입 변환(형 변환, Type Casting)

변수 타입을 변환하는 방법은 **자동 형 변환(Promotion)** 과 **명시적 형 변환(Casting)** 이 있음.

#### 1) 자동 형 변환(묵시적 변환, Widening)

* 작은 타입 ⇒ 큰 타입으로 변환할 때 자동 적용됨

```java
int num = 10;
double d = num; // 자동 변환(int => double)
```

#### 2) 명시적 형 변환(강제 변환, Narrowing)

* 큰 타입 ⇒ 작은 타입으로 변환할 때 반드시 명시해야 함.

```java
double pi = 3.14;
int intPi = (int) pi; //강제 변환(double -> int)
System.out.println(intPi); // result : 3
```
