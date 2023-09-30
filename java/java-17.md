# JAVA 17

## JAVA 17

* **text block**
* **Switch**
* **Record Data Class**
* **Sealed Class**
* **stream.toList()**

### Text Block

기존 JSON 문자열을 직접 생성하는 경우

```java
private static void before17() {

	String text = "{\\n"+
								" \\"name\\": \\"John Doe\\".\\n +
								" \\"age\\": 45,\\n" +
								" \\"address\\": \\"Doe Street, 23 , Java Town\\"\\n"+
								"}";

System.out.println(text);
}
```

가독성이 매우 떨어진다.

17 버전의 경우

```java
private static void java17() {
	String text ="""
			{
					"name": "John Doe",
					"age": 45,
					"address": "Doe Street, 23, Java Town"
			}
		""";

	System.out.println(text);
}
```

### Switch 표현식

주어진 열겨형의 값에 따라 작업을 수행하는 경우

스위치 표현식을 사용하여 스위치에서 값을 반환, 할당 등에서 값을 사용할 수 있다.

```java
//기존
 private static void oldStyle(Fruit fruit) {

	switch(fruit){

		case APPLE, PEAR:
			System.out.println("Common fruit");
			
			break;
		
		case ORANGE, AVOCADO:
			System.out.println("Exotic fruit");
			break;
		default:
			System.out.println("Undefined fruit);
	}
}
```

```java
//현재
private static void newStyle(Fruit fruit){
	
	switch(fruit) {
		case APPLE, PEAR -> System.out.println("Common fruit");
		case ORANGE, AVOCADO -> System.out.println("Exotic fruit");
		default -> System.out.println("Undefined fruit");
	}

}
```

반환값을 사용할 경우

```java
private static void returnValue(Fruit fruit) {
	String text = switch(fruit){

		case APPLE, PEAR -> "Common fruit";

		case ORANGE, AVOCADO -> "Exotic fruit";

		default -> "Undefined fruit";
	};
	System.out.println(text);
}
```

하나 이상의 동작을 수행 할 때

```java
private static void withYield(Fruit fruit) {
    String text = switch (fruit) {
        case APPLE, PEAR -> {
            System.out.println("the given fruit was: " + fruit);
            yield "Common fruit"; // 예약어 사용
        }
        case ORANGE, AVOCADO -> "Exotic fruit";
        default -> "Undefined fruit";
    };
    System.out.println(text);
}
```

### Records

롬복의 여러 기능을 Record를 사용하여 구현할 수 있음.

```java
public record GrapeRecord(Color color, int nbrOfPits) {
}

private static void basicRecord() {
    record GrapeRecord(Color color, int nbrOfPits) {}
    GrapeRecord grape1 = new GrapeRecord(Color.BLUE, 1);
    GrapeRecord grape2 = new GrapeRecord(Color.WHITE, 2);
    System.out.println("Grape 1 is " + grape1);
    System.out.println("Grape 2 is " + grape2);
    System.out.println("Grape 1 equals grape 2? " + grape1.equals(grape2));
    GrapeRecord grape1Copy = new GrapeRecord(grape1.color(), grape1.nbrOfPits());
    System.out.println("Grape 1 equals its copy? " + grape1.equals(grape1Copy));
}
```

### **Sealed Class**

Sealed Class, Interface는 간단하게 상속하거나(extends), 구현(implements)할 클래스를 지정해두고 해당 클래스들만 상속 혹은 구현을 허용하는 키워드

```java
public sealed interface CarBrand permits Hyundai, Kia{}

public final class Hyundai implements CarBrand {}
public non-sealed class Kia implements CarBrand {}
```

위 처럼 사용할수 있지만 위 코드에서 Hyundai, Kia 를 제외한 다른 클래스를 구현하려고 하면 에러발생

#### 제약사항

* **상속/구현하는 클래스는 final, non-sealed, sealed 중 하나로 선언되어야 한다.**
* **Permitted Subclass 들은 동일한 module에 속해야 하며 이름이 지정되지 않은 모듈에 선언 시에는 동일한 package 내에 속해야 한다.**
* **Permitted SUbclass는 Sealed Class 를 직접 확장해야한다.**

**어떠한 Super Class의 Sub Class들을 명확히 인지할 수 있어야 한다**는 것을 목표로 한다.

```java
String getBrandName(CarBrand brand) {
    if (brand instanceof Hyundai) {
    	return "Hyundai";
    }
    
    if (brand instanceof Kia) {
    	return "Kia";
    }
    
    // 예측할 수 없는 결과 발생
    return null;
}
```

CarBrand 인터페이스가 Sealed Interface가 아니라면 Subclass들을 일일이 체크해서 그에 따라 결괏값이 나뉘는 경우가 있다. 이때 if 분기 처리에서 처리하지 못한 어떠한 CarBrand의 Subclass에 대한 값의 처리를 해주어야 한다.

하지만 만약 CarBrand가 Sealed Interface라면 아래와 같은 코드로 변경할 수 있다.

```java
String getBrandName(CarBrand brand) {
    if (brand instanceof Hyundai) {
    	return "Hyundai";
    }
    
    return "Kia";
}
```

Hyundai 또는 Kia 둘 중 하나

### **stream.toList()**

참조 : [https://binux.tistory.com/146](https://binux.tistory.com/146)

#### 먼저 JDK 16

* **해당 기능은 JDK16 에 생김**
* **기존 Collect(Collectors.toList()) 의 부분을 toList() 메서드로 줄여서 간결화함.**

#### JDK 17

JDK16 의 collect(Collectors.toList())와 구현체가 다르게 업그레이드가 됨.

collect(Collectors.toList()) 는 ArrayList 를 반환.

toList() 는 Collectors.UnmodifiableList 또는 Collectors.UnmodifiableRandomAcccessList 를 반환한다.

둘의 차이는 이름에서 나타내는 것처럼 수정이 가능한 구현체와 수정이 불가능한 구현체라는 것이다.
