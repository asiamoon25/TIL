# Generic 과 Enum

## 1. Generic

### 1.1 타입 파라미터 문법

Java 의 **제너릭(Generic)** 은 클래스, 인터페이스, 메서드를 정의할 때 **타입 파라미터(type parameter)**  를 사용하여 다양한 타입에 대해 동작하는 일반화된 코드를 작성할 수 있게 해주는 기능임. 타입 파라미터는 보통 `<>` 꺽쇠 괄호로 표현하며, 클래스나 인터페이스 이름 뒤 또는 메서드 선언부에서 명시함. 예를 들어 JDK 의 `List<E>` 인터페이스는 요소의 타입을 나타내는 제네릭 타입 매개변수 `E` 를 선언하며, `Iterator<E>` 와 함께 제네릭으로 정의되어 있음. 또한 `Comparable<T>` 인터페이스 역시 `int compareTo(T o)` 와 같이 타입 파라미터 `T` 를 사용함. 이러한 `<T>` , `<K, V>` 와 같은 표기법은 각각 타입 파라미터의 이름을 나타내며, 여러 개일 때 쉼표로 구분함.

제네릭 타입은 사용 시에 구체적인 타입 인자를 제공하여 **파라미터화 된 타입** 을 만듬. 예를 들어 `List<String>` 은 `List<E>` 의 `E` 를 `String` 으로 지정한 파라미터화 타입 임. 클래스와 인터페이스에서는 아래와 같이 타입 파라미터를 선언하고 사용할 수 있음. 메서드에도 제네릭을 적용할 수 있는데, 메서드 리턴 타입 앞에 `<T>` 등을 명시하여 **제네릭 메서드** 로 선언함. (생성자에도 제네릭 타입 파라미터를 선언할 수 있음.)

```java
//제네릭 인터페이스 예시 : 입력 타입 F를 출력 타입 T로 변환하는 인터페이스
interface Converter<F, T> {
    T convert(F from);
}

// 제네릭 클래스 예시 : 두 개의 값을 쌍으로 보관하는 클래스
class Pair<K, V> {
    private K key;
    private V value;
    public Pair(K key, V value) {
        this.key = key;
        this.value = value;
    }
    public K getKey() { return key; }
    public V getValue() { return value; }
}

// 제네릭 메서드 예시 : 배열의 모든 원소를 출력하는 메서드
class GenericUtils {
    public static <T> void printArray(T[] arr) {
        for(T element : arr) {
            System.out.println(element);
        }
    }
}
```

위 코드에서 `Converter<F, T>` 는 인터페이스의 타입 파라미터 사용 예시이며, `Pair<K, V>` 는 클래스에 두 개의 타입 파라미터를 선언한 예임. `GenericUtils.printArray` 메서드는 `<T>` 라는 타입 파라미터를 선언하여, 메서드 내부에서 임의의 참조형 타입 배열을 받아 출력할 수 있도록 한 **제네릭 메서드** 임. 이처럼 제네릭 타입이나 메서드를 선언할 때 타입 매개변수를 타입이 필요한 위치(필드 타입, 메서드 파라미터 및 변환 타입 등) 에 사용할 수 있음. 실제 사용 시에는 `new Pair<String, Integer>("apple", 3)` 처럼 타입 인자를 구체적인 타입으로 지정해야 하며, 컴파일러는 제네릭 타입을 검사하여 타입 안정성을 보장해줌. (Java 7 부터는 생성자 호출 시 타입 추론이 가능해 `new Pair<>("apple", 3)` 와 같이 생성자쪽 꺽쇠를 생략하는 **다이아몬드 연산자** 도 사용할 수 있음.)

### 1.2 제약 조건과 bounded type(extends, 다중 bound 등)

제네릭 타입 파라미터는 **제약 조건(bound)** 을 지정하여 사용할 타입의 상한 또는 하한을 제한할 수 있음. **상한 경계(Upper Bound)** 를 지정할 때는 `extends` 키워드를 사용함. 예를 들어 `class NumberBox<T extends Number>` 라고 선언하면 이 클래스의 타입 파라미터 `T` 는 `Number` 의 하위 클래스들만 허용됨. 인터페이스에 대한 상한도 `extends` 로 표기하며, 여러 개의 상한을 지정하는 **다중 bound** 도 가능함. 예를 들어 `<T extends Number & Comparable<T>>` 라고 하면 `T` 는 `Number` 를 상속받고 동시에 `Comparable<T>` 인터페이스를 구현하는 클래스여야 함. 다중 bound 를 지정할 때는 클래스는 한 번만 지정할 수 있고(`Number` 같은 클래스 상한을 둘 이상 나열할 수 없음), 클래스 상한을 제일 앞에 적어야 함. 인터페이스 상한은 여러 개 지정할 수 있으며, `&` 기호로 연결함.

타입 파라미터에 상한을 지정하면 그 타입으로 사용할 수 있는 멤버나 메서드가 제한에 따라 달라짐. 예를 들어 `<T extends Number>` 로 선언된 `T` 는 `Number` 의 메서드들(`intValue()` , `doubleValue()` 등) 을 사용할 수 있음. 반면 **하한 경계(Lower Bound)** 는 타입 파라미터 선언에서는 직접 사용할 수 없고(제네릭 타입 매개변수에 `super` 를 사용할 수는 없음.) 하한은 주로 와일드카드에서 사용됨.(아래 **와일드 카드** 섹션 참조). 정리하면, 타입 변수의 제한에는 `extends` 를 사용하고, Java 제네릭에서는 클래스든 인터페이스든 제한에는 모두 `extends` 를 쓴다는 점에 유의해야함. (`implements` 를 쓰지 않으며, `super` 는 타입 변수 선언에서는 허용되지 않음)

다음은 타입 파라미터에 상한을 적용한 클래스 예시임. `RestrictedBox` 는 타입 매개변수 `T` 를 `Number` 의 하위이면서 `Comparable<T>` 를 구현한 타입으로 제한하고 있음. 따라서 `T` 는 숫자 타입(Number 하위) 이며 서로 비교 가능하므로, 클래스 내부에서 `compareTo` 를 사용할 수 있음.

```java
// 다중 상한(Bound) 을 가지는 제네릭 클래스 예시
class RestricedBox<T extends Number & Comparable<T>> {
    private T value;
    public RestrictedBox(T value) {
        this.value = value;
    }
    public T getValue() {
        return value;
    }
    // 값과 다른 T 값의 크기를 비교하는 메서드 (Comparable<T> 사용 가능)
    public int compareTo(T other) {
        return value.compareTo(other);
    }
}
```

위 코드처럼 `<T extends 상위타입>` 형태로 선언하면 T는 해당 상위타입으로 제한됨. 만약 T 에 여러 조건을 걸고 싶다면 `<T extends ClassA & InterfaceB & InterfaceC>` 와 같이 선언함. **주의 :** 상한에 클래스는 하나만 지정 가능하며, 클래스와 인터페이스를 함께 지정했다면 클래스 상한을 먼저 명시해야 함.

### 1.3 와일드카드(?, ? extends, ? super)

제네릭을 사용하다 보면, 구체적인 타입 인자를 지정하지 않고, **와일드카드(?)** 로 표현해야 하는 경우가 있음. 와일드카드는 제네릭 타입을 사용할 때 그 타입이 **어떤 타입인지 확실하지 않거나 상관 없음** 을 나타내는 용도로, `?` 단독으로 또는 `extends` , `super` 키워드와 함께 사용함.

* `?` (**무제한 와일드 카드**) : 특정 제한 없이 "어떤 타입이든 될 수 있다." 는 의미를 가짐. 예를 들어 `List<?>` 는 "아무 타입의 객체를 담을 수 있는 리스트" 를 뜻함. `List<?>` 는 사실상 `List<? extends Object>` 와 동일함. 와일드카드를 제한 없이 사용할 때는 주로 메서드 내부에서 타입에 의존하지 않는 로직을 처리하거나, 제네릭 타입 간의 관계를 강제할 필요가 없을 때임. 무제한 와일드카드일 경우 **데이터를 추가 삽입할 수 없고** (타입을 알 수 없으므로 컴파일러가 막음), 가져올 때는 `Object` 타입으로만 얻을 수 있음.
* `? extends T` (**상한이 있는 와일드카드**) : 구체적인 타입 인자는 모르지만 **어떤 특정 타입** `T` **의 하위 타입** 으로 제한하고 싶을 때 사용함. `? extends T` 와일드 카드를 사용한 제네릭 타입은 해당 타입에서 **값을 꺼내(read) 볼 때 안전하게** `T` **로 취급** 할 수 있다는 장점이 있음. 예를 들어 `List<? extends Number>` 는 `Number` 의 하위 타입들로 구성된 리스트를 가리킬 수 있는데, 이 리스트에서 요소를 꺼내면 항상 `Number` 로 받아들일 수 있음. 그러나 컴파일러는 리스트에 새로운 요소를 추가하는 것은 막음. 왜냐하면 `List<? extends Number>` 가 가리키는 실제 리스트가 `List<Integer>` 인지 `List<Double>` 인지 알 수 없기 때문에, 임의의 `Number` 를 넣는 작업은 타입 불일치 위험이 있기 때문임. 따라서 `? extends T` 를 쓰면 **읽기 전용** 으로 사용하게 되고, 추가 삽입은 할 수 없음.(null 추가 정도만 예외적으로 허용되지만 특별한 경우를 제외하면 활용되지 않음)
* `? super T` ( **하한이 있는 와일드 카드** ) : **어떤 특정 타입** `T` **의 상위 타입** 으로 제한하고 싶을 때 사용함. `? super T` 와일드카드를 사용한 제네릭 타입은 해당 타입에 **값을 추가(write) 할 때 안전하게** `T` **타입으로 넣을 수 있다** 는 특징이 있음. 예를 들어 `List<? super Integer>` 는 `Integer` 의 상위 타입 리스트(`List<Number>` , `List<Object>` 등)를 가리킬 수 있는데, 이 리스트에 `Integer` 값을 추가하는 것은 타입 안전함. 왜냐하면 실제 리스트가 `Number` 나 `Object`  타입의 리스트라면 `Integer` 는 그 하위로서 저장될 수 있기 때문임. 하지만 반대로 꺼내올 때는 타입이 모호하므로 `Object` 로만 얻을 수 있음. 따라서 `? super T` 를 쓰면 **쓰기 전용** 목적으로 활용되며, 요소를 읽어서 사용할 때는 즉시 `T` 타입임을 보장하지 못함.(필요하다면 캐스팅이 필요함.)

이러한 와일드카드 사용 패턴을 요약하는 유명한 원칙이 **PECS(Producer Extends, Consumer Super)** 임. 즉, **데이터를 생산(제공) 하는 매개변수는** `? extends` **로 받고, 소비(추가 저장) 하는 매개변수는** `? super`  **로 받으라는** 가이드라인임. 만약 한 변수에서 데이터 입력과 출력 역할을 모두 해야 한다면 와일드카드를 쓰지 않고 타입 파라미터 자체를 사용하는 것이 안전함.

> **와일드카드 활용 요약** :&#x20;
>
> * `List<? extends T>` : `T` 의 하위 타입들을 담는 리스트. **읽기** 는 `T` 로 안전하게 가능, **쓰기** (추가)는 불가능.(Producer 용도)
> * `List<? super T>` : `T` 의 상위 타입들을 담는 리스트. **읽기** 시에는 `Object` 로만 획득, **쓰기** 는 `T` (또는 그 하위) 객체 추가 가능.(Comsumer 용도)
> * `List<?>` : 불특정 타입의 리스트. `Object` 이외 구체적 타입으로 **읽기** 불가, **쓰기** 불가.(`List<? extends Object>` 와 동일)

와일드 카드의 동작 예제

아래 코드에서 `? extends Number` 와 `? super Integer` 리스트의 차이를 보여줌.

```java
List<Integer> intList = Arrays.asList(1, 2, 3);
List<? extends Number> numList = intList;
Number num = numList.get(0);        // OK : Number 로 읽기
// numList.add(4);                  // 컴파일 오류 : extends 와일드카드는 추가 불가

List<Object> objList = new ArrayList<>();
List<? super Integer> superList = objList;
superList.add(100);                // OK : Integer 추가 가능
Object obj = superList.get(0);     // OK : Ojbect 로 읽기( Integer 임을 알 수 없음)
```

위 코드에서 `numList` 는 `Integer` 리스트를 받았지만 `List<? extends Number>` 로 선언되었기 때문에 `Number` 로 값 읽기(`get` ) 가 가능함. 그러나 `add` 메서드는 컴파일 에러인 것을 볼 수 있음. 반대로 `superList` 는 `Object` 리스트를 가리키지만 `List<? super Integer>` 타입으로 선언되었기 때문에 `Integer` 값을 추가(`add` ) 하는 것은 허용됨. 다만 `get` 으로 가져온 값은 `Object` 타입으로만 얻어짐. 이처럼 와일드카드는 제네릭 타입을 보다 유연하게 다루게 해주지만, `extends` 와 `super` 에 따라 **읽기/쓰기의 제약이 다르므로** 위 원칙에 따라 사용하는 것이 좋음.

### 1.4 타입 소거(Type Erasure)와 제네릭의 한계

Java 의 제네릭은 **컴파일 타임** 에만 유효하고 **런타임에는 타입 정보가 소거** 됨. 이를 **타입 소거(type erasure)** 라고 부르며, Java 컴파일러는 제네릭을 구현하기 위해 컴파일 시 다음과 같은 작업을 수행함.

* 모든 제네릭 타입 변수들을 그 **상한 경계 타입이나 Object 로 대치** 함.(상한 경계가 없다면 `Object` 로 치환). 예를 들어 컴파일러는 `List<String>` 과 `List<Integer>` 를 둘 다 내부적으로는 단순한 `List` (혹은 `List<Object>` ) 로 간주함.
* 제네릭으로 인해 필요해진 타입 캐스팅 코드를 적절히 삽입하여 타입 안정성을 유지함.
* 제네릭 타입을 상속하여 하위에서 형변환되는 경우 _bridge method_ 등을 생성하여 다형성이 제대로 유지되도록 함.

그 결과 **바이트코드에는 제네릭 타입 정보가 들어가지 않으며** , 런타임에는 제네릭 타입 인자에 따른 별도의 클래스가 생성되지 않음. 따라서 **제네릭 자체로 인한 런타임 오버헤드는 존재하지 않음** . 이는 C++의 템플릿과 달리, Java 의 제네릭은 컴파일 시에만 동작하고 런타임에 영향이 없다는 중요한 특징임.

하지만 타입 소거로 인해 **몇 가지 제약사항과 주의점** 이 생김.

* `instanceof` **와 캐스팅의 제한** : 런타임에는 제네릭 타입 인자 정보가 지워지므로, 특정 제네릭 타입으로의 `instanceof` 검사는 불가능함. 예를 들어 `obj instanceof List<String>` 와 같은 코드는 컴파일 오류임. 이는 JVM 이 런타임에 `List<String>` 과 `List<Integer>` 를 구분할 수 없기 때문임. 실제로 `new ArrayList<String>()` 로 만든 리스트와 `new ArrayList<Integer>()` 로 만든 리스트의 클래스타입을 출력해보면 둘 다 `java.util.ArrayList` 로 나타남. 예를 들어:

```java
List<String> listA = new ArrayList<>();
List<Integer> listB = new ArrayList<>();
System.out.println(listA.getClass() == listB.getClass()); // true(둘다 ArrayList)
```

위 결과에서 두 리스트의 클래스가 동일(`ture` ) 함을 확인할 수 있음. 즉, **런타임에는 제네릭 타입 인자의 구분이 없는** 것임. 따라서, `instanceof ArrayList<String>` 같은 코드는 의미가 없으며 컴파일러가 이를 금지함. `instanceof` 는 오직 원시(raw) 타입이나 한정되지 않은 와일드카드(`ArrayList<?>` ) 등에 대해서만 사용할 수 있음.

* **제네릭 타입의 배열 생성 불가** : 제네릭 타입 정보는 런타임에 소거되므로, **제네릭 타입의 배열** 을 생성할 수 없음. 예를 들어 `List<String>[] array = new List<String>[10];` 과 같은 코드도 컴파일 시 오류가 발생함. 이는 배열이 런타임에 자신이 담는 원소의 타입을 확인하는 특징(공변성) 이 있는데, 제니릭 타입의 배열의 경우 이ㅏ를 검사할 방법이 없기 때문임. 만약 제네릭 배열이 허용된다면, 배열의 타입 안정성을 깨뜨릴 수 있는 상황이 생겨도 런타임에 예외를 발생시키지 못하고 잘못된 캐스팅 문제가 발생할 수 있음. 이러한 이유로 Java 에서는 **비검증(unchecked) 캐스트 경고** 를 감수하고 `Object` 배열을 생성한 뒤 제네릭 타입으로 캐스팅하는 우회 방법을 쓰지 않는 한, 직접적인 제네릭 배열 생성은 금지됨.
* **기타 제한** : 제네릭 타입의 정보가 소거되므로, **클래스의 static 필드에는 타입 변수 사용이 불가능** 함. static 필드는 클래스 수준에서 단 하나만 존재하는데, 타입 매개변수는 인스턴스별로 달리질 수 있으므로 모순이 생기기 때문임. 또한 **예외 클래스(Throwable 하위)에 제네릭을 적용** 할 수 없으며, **제네릭 타입을 throws 절에 사용하거나 catch 절에 타입 변수로 예외를 잡는 것** 도 제한됨. 메서드 오버로딩 시에도 타입 소거 결과 시그니처가 같아지는 경우 오버로딩이 성립되지 않음.( 예를 들어, `void method(List<String> x)` 와 `void method(List<Integer> x)` 는 둘 다 `void method(List x)` 로 소거되므로 동시에 정의할 수 없음).

요약하면, Java 의 제네릭은 컴파일러가 강한 타입 검사를 해주는 문법적인 장치이며, 런타임에는 모든 제네릭 타입이 **원시 타입(raw type) 으로 취급** 됨. 이러한 구현으로 성능상 이점이 있지만, 위와 같은 하계에 유의해아 함. 특히 `instanceof` 나 배열 생성 시에는 제네릭 타입 정보를 사용할 수 없다는 점을 기억해야 함.

```java
List<String> list = new ArrayList<>();
// if (list instanceof ArrayList<String>) { ... }  // 컴파일 오류: 제네릭 타입으로 instanceof 불가

// List<String>[] listArray = new List<String>[5]; // 컴파일 오류: 제네릭 배열 생성 불가

// (주의) 제네릭 타입의 런타임 타입 확인은 불가능 
System.out.println(list.getClass());            // 출력 예: class java.util.ArrayList  (타입 인자 불명)
```

위 코드에서 보듯이, `instanceof ArrayList<String>` 같은 검사는 허용되지 않으며 제네릭 배열 생성도 컴파일이 막음. 마지막 출력 결과에서 `list` 의 실제 클래스 타입은 `ArrayList` 로만 나타나고, `<String>` 정보는 확인할 수 없음. 이처럼 **제네릭은 오로지 컴파일 시점의 타입 안정성을 위한 도구** 이며, 런타임에는 타입이 지워진다는 사실을 이해해야함.(필요하다면 리플렉션 등의 기법으로 일부 제네릭 타입 정보 (`Class<T>`  인수 등) 를 다룰 수 있지만, 일반적인 상황에서는 타입 소거로 인해 제네릭 타입 인자는 런타임에 활용되지 않음.)

## 2. 열거형(Enum)

### 2.1 기본 문법과 사용 예

Java 의 **열거형(enum)** 은 몇 가지 **상수들의 집합** 을 하나의 타입으로 표현하는 특별한 클래스임. enum 은 클래스와 유사하게 정의하지만, 미리 정해진 여러 **상수(constants)** 를 나열하여 그 값들만 가질 수 있도록 함. 선언 문법은 `enum 열거형 이름 { 상수1, 상수2, ... }` 형태로 사용함. 예를 들어 요일을 나타내는 열거형을 다음과 같이 정의할 수 있음.

```java
public enum Day {
    SUNDAY, MONDAY, TUESDAY, WEDNESDAY, THURSDAY, FRIDAY, SATURDAY
}
```

이렇게 열거된 이름들이 `Day` enum 타입의 **상수** 들이며, 각각 `Day.SUNDAY` , `Day.MONDAY` 처럼 사용할 수 있음.

사실 enum 으로 선언된 각 상수는 해당 enum 타입의 **public static final 객체** 로 간주됨. Enum 타입은 내부적으로 `java.lang.Enum` 클래스를 상속하며, 다른 클래스를 상속할 수는 없음.(자바 언어에서 다중 상속을 지원하지 않으므로, 이미 Enum 을 상속한 열거형은 추가 상속이 불가능함.) 대신 인터페이스를 구현하거나 필드, 메서드, 생성자를 가질 수 있어서, 단순한 상수 모음 이상의 기능을 제공함.



컴파일러는 enum을 컴파일할 때 몇 가지 편의 기능을 자동으로 제공함. 그 중 자주 쓰이는 것이 `values()` **메서드** 임. 모든 enum 타입은 **모든 상수를 담은 배열을 반환하는 static T\[] values() 메서드** 를 가지며, 상수 선언 순서에 따라 배열에 담아 돌려줌. 이를 통해 열거형의 모든 값들을 순회(iterate) 할 수 있음. 또한 `valueOf(String name)` 메서드를 자동 생성하여, 해당 enum 타입에서 **주어진 이름과 일치하는 상수** 를 반환해줌. 예를 들어 `Day.valueOf("MONDAY")` 는 `Day.MONDAY` 상수를 돌려줌. 이때 인자로 전달한 문자열과 동일한 이름의 상수가 없다면 `IllegalArgumentException` 이 발생함.

그 외에도, 각 enum 상수는 `ordinal()` **메서드** 를 가지고 있는데, 이는 해당 상수가 열거형 선언에서 정의된 순서 index 를 의미하는 정수를 반환함. 예를 들어 `Day.SUNDAY.ordinal()` 이 `0`, `Day.MONDAY.ordinal()` 이 `1` 을 반환하는 식임. ( 첫 상수의 ordinal 이 0부터 시작). 이 ordinal 값은 상수의 순서를 나타내지만, **열거형 상수의 순서는 변경될 수 있으므로 ordinal 값을 비즈니스 로직에 직접 사용하는 것은 권장되지 않음** . 대신 상수의 고유한 의미가 필요하면 별도의 값을 필드로 정의하는 것이 좋음. 마지막으로 `name()` 메서드는 상수 선언명을 그대로 문자열로 반환함. 이 값은 열거형 선언에서의 이름과 정확하게 일치하며, 기본적으로 `toString()` 메서드도 같은 값을 반환하지만 필요에 따라 `toString()` 을 오버라이드하여 출력용으로 다른 문자열을 제공할 수도 있음.

**예시코드**&#x20;

간단한 레벨(Level) 열거형을 정의하고, 제공되는 메서드들을 활용한 예임.

```java
enum Level { LOW, MEDIUM, HIGH }

for(Level lv : Level.values()) {
    System.out.println(lv + " - ordinal: " + lv.ordinal());
}

Level selected = Level.valueOf("HIGH");
System.out.println("Name: " + selected.name());
```

**실행 결과 예:**

```yaml
LOW - ordinal: 0
MEDIUM - ordinal: 1
HIGH - ordinal: 2
Name: HIGH
```

위 예에서 `Level.values()` 를 통해 `[LOW, MEDIUM, HIGH]` 순서의 상수 목록을 얻어 `for-each` 로 순회했고, 각 상수의 이름과 `ordinal()` 값을 출력했음. 그 다음 `Level.valueOf("HIGH")` 를 이용해 문자열 "HIGH" 에 대응되는 상수를 얻어내어, 그 `name()` 을 출력했음

* `values()` : **모든 상수** 를 정의 순서대로 담은 배열을 반환&#x20;
* `valueOf(String)` : **이름과 일치하는 상수** 를 반환.(대소문자 정확히 일치해야 하며, 없으면 예외)
* `ordinal()` : **상수의 순서 인덱스** (0부터 시작) 를 반환.
* `name()` : **상수의 이름 문자열** 을 반환. (주로 디버깅이나 저장용으로 사용, 필요시 `toString()` 오버라이드 가능)

이 외에도 enum 은 `Enum` 클래스의 메서드인 `compareTo()` (정의 순서 비교)나 `equals()` , `hashcode()` 등이 기본 제공되며, `switch` 문에서도 열거형 상수를 case 레이블로 사용할 수 있는 등 다양한 편의가 있음.

### 2.2 상수별 메서드 오버라이딩( 전략 패턴 응용)

Java 의 열거형은 각 상수마다 **각기 다른 동작을 구현** 할 수 있는 강력한 기능을 가지고 있음. 사실 열거형은 클래스이기 때문에 내부에 메서드를 정의할 수 있고, **각 enum 상수는 해당 enum 클래스의 인스턴스** 이므로 필요한 경우 **상수별로 메서드를 오버라이드** 할 수 있음. 이를 활용하면 하나의 enum 상수마다 서로 다른 행동 규칙을 부여할 수 있는데, 이러한 패턴은 흔히 **전략 패턴(Strategy Pattern)** 을 열거형으로 구현한 것과 유사함.

상수별로 메서드를 다르게 구현하는 방법으로는 **추상 메서드 선언 후 상수별 구현 제공** 을 많이 사용함. 즉, enum 클래스 안에 추상 메서드를 선언하고, 각 상수가 그 메서드를 구체적으로 오버라이드 하도록 만드는 것임. 이렇게 하면 enum 상수를 추가하는 것만으로 새로운 동작을 쉽게 확장할 수 있고, 별도의 `switch` 문이나 분기 없이 다형성을 활용할 수 있음. 아래는 간단한 계산기 연산을 enum 으로 구현한 예시임. `Operation` 열거형은 사칙연산을 나타내는 상수들을 가지며, 추상 메서드 `apply` 를 선언하고 각 상수마다 이를 구현함.

```java
enum Operation {
    PLUS {
        @Override
        public int apply(int x, int y) { return x + y; }
    },
    MINUS {
        @Override
        public int apply(int x, int y) { return x - y; }
    }
    MULTIPLY {
        @Override
        public int apply(int x, int y) { return x * y; }
    }
    DIVIDE {
        @Override
        public int apply(int x, int y) { return x / y; }
    }
    
    public abstract int apply(int x, int y);
}

// 사용 예
System.out.println(Operation.PLUS,apply(3,4)); // result : 7
System.out.println(Operation.DIVIDE.apply(8,2)); // result : 4
```

위 코드에서 `Operation` enum 은 `apply(int, int)` 라는 추상 메서드를 가지고 있고, 각 상수(PLUS, MINUS, MULTIPLY, DIVIDE) 가 자신만의 방식으로 이 메서드를 구현하고 있음. `Operation.PLUS.apply(3,4)` 는 내부적으로 PLUS 상수에 구현된 코드를 실행하여 3 + 4 결과를 반환하고, `Operation.DIVIDE.apply(8,2)` 는 DIVIDE 상수의 구현을 실행함. 이처럼 **각 상수가 독립적인 로직을 가질 수 있으므로**, 새로운 연산을 추가하려면 enum 에 상수를 추가하고 해당 상수에 메서드를 구현하기만 하면 됨. 별도의 조건분기 코드 변경 없이도 확장이 가능하며, 열거형 자체가 연산 집합과 동작을 캡슐화하므로 코드가 깔끔해짐. 이것은 일종의 enum 을 사용한 전략 패턴 구현으로 볼 수 있으며, 대표적인 활용 사례임. (Java API 에서도 `java.math.RoundingMode` 의 각 상수가 `round` 메서드를 다르게 구현하는 등 유사한 기법이 활용되어 있음)

만약 enum 에서 공통 메서드를 각 상수가 서로 다르게 동작하도록 하고 싶다면 꼭 추상 메서드가 아니어도 됨. 필요한 메서드를 정의하고, 기본 구현을 넣은 뒤 특정 상수에서만 이를 오버라이드 할 수도 있음. 하지만 대부분의 경우 위처럼 추상 메서드를 선언하는 패턴이 많이 쓰임. 이러한 기능 덕분에 **열거형 상수 각각이 하나의 클래스처럼 동작하도록 만들 수 있고** , 관련된 상태나 메서드를 가질 수도 있음.

### 2.3 열거형의 인터페이스 구현

열거형은 내부적으로 Enum 을 확장한 **클래스** 이므로, 다른 일반 클래스처럼 **인터페이스를 구현(implements)** 할 수도 있음. 열거형이 인터페이스를 구현하면, 그 인터페이스 타입으로 열거형 상수를 다룰 수 있어 다형성이 증가하고, 열거형 상수들에게 특정 행위를 강제할 수 있다는 장점이 있음. 인터페이스를 구현하는 enum 은 해당 인터페이스의 추상 메서드를 구현해야 하는데, 이 구현을 **각 상수별로 다르게** 할 수도 있고, 공통되게 할 수도 있음. 상수별로 다르게 하려면 앞서 설명한 것처럼 enum 내에 추상 메서드를 선언하거나 인터페이스의 메서드를 추상 메서드화하여 각 상수가 오버라이드하게 만들면 됨.

예를 들어, 간단한 인터페이스를 enum 으로 구현해봄. 다음 코드는 `Greeting` 이라는 인터페이스를 만들고, `GreetingType` 열거형이 이를 구현한 사례임. 각 상수는 `greet()` 메서드를 자기만의 방식으로 오버라이드하고 있음.

```java
interface Greeting {
    void greet();
}

enum GreetingType implements Greeting {
    HELLO {
        @Override
        public void greet() {
            System.out.println("Hello!");
        }
    },
    GOODBYE {
        @Override
        public void greet() {
            System.out.println("Goodbye!");
        }
    }
}

// 사용 예
Greeting hello = GreetingType.HELLO;
hello.greet();        // result : Hello
GreetingType.GOODBYE.greet(); // result Goodbye!
```

위에서 `GreetingType` 열거형은 `Greeting` 인터페이스를 구현했으므로, `GreetingType` 의 모든 상수는 `Greeting` 타입으로 취급될 수 있음. `HELLO` 와 `GOODBYE` 상수 각각이 `greet()` 메서드의 구현을 제공하여, 호출 시 서로 다른 메시지를 출력함. 이처럼 **열거형이 인터페이스를 구현하면 각 상수를 인터페이스의 구현체로 활용** 할 수 있음. 예를 들어 인자로 `Greeting` 타입을 받는 메서드에 `GreetingType.HELLO` 를 전달할 수 있고, 이는 다형적으로 동작함.

열거형은 여러 인터페이스를 구현할 수도 있음. 참고로, Java 의 모든 enum 은 컴파일 시 자동으로 `java.lang.Enum` 을 상속하고 `java.lang.Comparable` 과 `java.io.Serializable` 인터페이스를 구현하도록 되어 있음. 따라서 enum 간 비교나 직렬화 등이 기본적으로 가능하며, 추가로 개발자가 원하는 인터페이스를 구현하여 기능을 확장할 수 있음. 인터페이스를 통해 enum 상수의 행동을 추상화하면, enum 을 사용하는 코드 입장에서 구현을 몰라도 해당 인터페이스 메서드를 호출하여 일관된 동작을 이끌어낼 수 있다는 이점도 있음.

### 2.4 EnumSet 과 EnumMap

Java Collections Framework 는 열거형과 함께 쓰기에 최적화된 컬렉션 구현체로 **EnumSet** 과 **EnumMap** 클래스를 제공함. 이들은 내부 구현을 열거형에 특화하여, 열거형 상수를 다룰 때 성능과 메모리 효율을 높일 수 있는 자료구조임. **열거형 타입 전용** 이라는 제약이 있지만, 그만큼 일반적인 `Set` 이나 `Map` 구현보다 뛰어난 최적화가 이루어져 있음.

*   **EnumSet** :&#x20;

    `EnumSet<E extends Enum<E>>` 은 특정 열거형 타입의 값만 담을 수 있는 집합(Set) 구현체임. 모든 원소가 동일한 enum 타입이어야 하며, 내부적으로 **비트 벡터(bit vector)** 로 원소들을 관리함. 각 열거형 상수에 하나의 비트가 대응되며, 상수가 EnumSet 에 포함되면 그 비트가 1로 설정되는 식의 매우 컴팩트한 표현을 사용함. 이러한 구조 덕분에 **메모리 공간** 을 효율적으로 쓰고 **연산 속도도 매우 빠름** . 비트 연산으로 구현되어 있으므로 합집합, 교집합 등의 집합 연산도 빠르게 수행되며, 원소의 존재 여부 체크, 추가/삭제 등이 **상수 시간 O(1)** 에 가능함. EnumSet 은 **전통적인 정수 비트 마스크를 대체하는 타입 안전한 대안** 으로 권장되며, 열거형 상수들을 집합으로 다루어야 할 때 최상의 성능을 제공함.\
    EnumSet 을 생성할 때는 일반적으로 정적 팩토리 메서드를 사용함. 예를 들어 `EnumSet.of(A, B)` 는 주어진 상수들로 구성된 EnumSet을 만들고, `EnumSEt.allOf(EnumType.class)` 는 해당 열거형의 모든 상수를 포함하는 EnumSet 을 만듬. `EnumSet.noneOf(EnumType.class)` 는 비어있는 EnumSet 을 생성함. 다음은 EnumSet  사용 예시임.

```java
enum Day { MON, TUE, WED, THU, FRI, SAT, SUN }

EnumSet<Day> weekend = EnumSet.of(Day.SAT, DAY.SUN);
System.out.println(weekend);        // result : [SAT, SUN]

EnumSet<Day> workdays = EnumSet.complementOf(weekend);
System.out.println(workdays);       // result : [MON, TUE, WED, THU, FRI]
```

위 코드에서 `EnumSet.of(Day.SAT, Day.SUN)` 은 `SAT` 와 `SUN` 만 담은 EnumSet 을 생성하고 `complementOf` 메서드는 주어진 EnumSet 에 포함되지 않는 열거형 값들로 구성된 새로운 EnumSet 을 반환함. 따라서 `workdays` 는 주말을 제외한 요일들로 채워짐. EnumSet 은 내부적으로 비트 연산을 활용하므로, 원소 개수가 많아도 빠르게 동작하며, **HashSet 대비 성능상 이점** 이 큼. 단, EnumSet 은 enum 타입당 하나의 비트셋으로 표현되므로 **동시에 여러 enum 타입을 혼합하여 저장할 수 없으며**, `null` 값도 허용되지 않음. (null 을 넣으려고 하면 `NullPointerException` 발생). 대부분의 경우 이러한 제한은 문제가 되지 않으며, 열거형 상수의 집합을 다룰 때 EnumSet 을 사용하는 것이 권장됨.



*   **EnumMap** :&#x20;

    `EnumMap<K extends Enum<K, V>` 는 **열거형을 키로 사용하는 전용 Map 구현체** 임. EnumMap의 키는 모두 같은 enum 타입이어야 하며, 내부적으로 **배열** 을 이용해 구현되어 있음. 열거형 상수의 `ordinal()` 값을 배열 index 로 사용하여 대응되는 값을 저장하므로, 해싱(Hashing) 을 사용하지 않고도 **매우 빠른 조회/갱신 성능** 을 보임. 기본연산(조회, 추가, 삭제 등) 이 **상수 시간** 에 실행되며, 일반적인 HashMap 보다도 효율적임. 또한 배열 기반 구현 덕분에 메모리도 열거형 요소 개수에 딱 맞춰 사용할 수 있어 낭비가 적고 **메모리 사용이 컴팩트** 함.\
    \
    EnumMap 을 생성할 대는 키로 사용할 enum 의 `Class` 객체가 필요함. 예를 들어 `new EnumMap<>(Day.class)` 처럼 enum 타입을 지정하면 해당 enum 의 모든 값을 담을 수 있는 내부 배열이 설정됨.(초기에는 값이 비어있고, 필요한 키에 대해서만 값이 채워짐.) EnumMap 은 iterator(순회) 시 **열거형 정의 순서(자연순서)** 를 유지하는 것도 특징임. 이는 HashMap 이 해시값 순서로 키를 배치하는 것과 달리, EnumMap 은 열거형 상수의 순서를 따르므로 예측 가능한 순서를 보임. 다음은 EnumMap 사용 예시임.

```java
EnumMap<Day, String> dayType = new EnumMap<>(Day.class);
dayType.put(Day.MON, "Weekday");
dayType.put(Day.SAT, "Weekend");

System.out.println(dayType.get(Day.MON));        // result : Weekday
System.out.println(dayType);                     // result {MON=Weekday, SAT=Weekend}
```

위 코드에서 `dayType` 은 `Day` 열거형을 키로 하고 문자열을 값으로 가지는 EnumType 임. `Day.MON` 키에 `"Weekday"` 를 넣고, `Day.SAT` 키에 `"Weekend"` 를 넣은 후 조회와 출력 예를 보여줌. 출력 결과를 보면 `{MON=Weekday, SAT=Weekend}` 와 같이 키의 선언 순서(월요일이 토요일보다 앞선 순서) 가  유지되어 나타남.

EnumMap 은 **모든 키를 배열로 직접 접근** 하므로 매우 빠르며, 키의 전체 갯수가 열거형 상수의 수로 한정되기 때문에 HashMap 처럼 용량이 동적으로 늘어나는 과정도 없음. 따라서 **열거형을 키로 사용해야 하는 상황에서는 항상 EnumMap 을 우선적으로 고려** 하는 것이 좋음. 예를 들어 열거형 상수를 어떤 값에 매핑하는 경우 (`switch` 문으로일일이    분기하는 대신) EnumMap 에 상수-값 쌍을 넣어 관리하면 코드가 깔끔해지고 성능도 우수함. EnumMap 도 EnumSet 과 마찬가지로 null 키를 사용할 수 없지만 null 값은 허용함. 그리고 **thread-safe 하지 않으므로** 여러 쓰레드에서 동시 수정이 필요하면 별도로 동기화하거나 `Collections.synchronizedMap` 으로 감싸야함.
