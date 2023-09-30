# Entity 와 Value

## Entity

#### 특징

**식별자를 가짐**

| Order                           |
| ------------------------------- |
| - orderNumber : String          |
| - orderLines : List\<OrderLine> |
| - totalAmounts : int            |
| - ShippingInfo : ShippingInfo   |
| - state : OrderState            |

위 처럼 각 주문들은 주문번호를 갖는데 각 주문마다 서로 다르다.(UID 느낌)

엔티티의 식별자는 바뀌지 않고 고유하기 떄문에 두 엔티티 객체의 식별자가 같으면 두 엔티티는 같다고 볼 수 있다.

이렇게 식별자를 이용해서 .equals(), hashCode() 메서드를 구현할 수 있음.

```java
public class Order {
	private String orderNumber;

	@Override
	public boolean equals(Oject obj) {
		if (this == obj) return true;
		if (obj == null) return false;
		if (obj.getClass() != Order.class) return false;
		Order other = (Other)obj;
		if ( this.orderNumber == null) return false;
		return this.orderNumber.equals(order.orderNumber);
	}

	@Override
	public int hashCode() {
		final int prinme = 31;
		int result = 1;
		result = prin * result + ((orderNumber == null) ? 0 : orderNumber.hashCode());
		return result;
	}
}
```

#### Entity 식별자 생성

생성 시점은 도메인의 특징과 사용하는 기술에 따라서 달라짐.

식별자 생성 방식

* 특정 규칙에 따라 생성
* UUID 사용
* 값을 직접 입력
* 일련번호 사용(시퀀스나 DB의 자동 증가 컬럼 사용)

**식별자 생성 규칙**

*   시간 + 값 조합

    주의할 점은 같은 시간에 동시에 만들어지는 식별자는 동일해서는 안된다.
*   UUID(Universally unique identifier)

    ```java
    UUID uuid = UUID.randomUUID();
    String strUuid = uuid.toString();
    ```
*   일련 번호 사용

    주로 DB에서 제공하는 자동 증가 컬럼

    ```java
    String orderNumber = orderRepository.generate();

    Order order = new Order(orderNumber,...);
    orderRepository.save(order);
    ```

    ## Value Type

    ```java
    public class ShippingInfo {

    	private String receiverName;
    	private String receiverPhoneNumber;

    	private String shippingAddress1;
    	private String shippingAddress2;
    	private String shippingZipcode;
    	...
    }
    ```

    위 클래스의 recieverName 필드와 receiverPhoneNumber 필드는 서로 다른 데이터를 담고 있지만 개념적으로는 받는 사람을 의미한다.

    → 하나의 개념을 표현하고 있음.

    마찬가지로 Address1,2 Zipcode 필드도 하나의 개념을 표현함.

    **Value Type 은 개념적으로 완전한 하나를 표현할 때 사용함.**

    ```java
    public class Receiver {
    	private String name;
    	private String phoneNumber;

    	public Receiver ( String name, String phoneNumber) {
    		this.name = name;
    		this.phoneNumber = phoneNumber;
    	}

    	public String getName() {
    		return name;
    	}

    	public String getPhoneNumber() {
    		return phoneNumber;
    	}
    }
    ```

    Reciever 는 ‘받는 사람’ 이라는 도메인 개념을 표현함.

    위의 ShippingInfo의 recieverName 필드와 receiverPhoneNumber 필드가 필드 이름을 통해서 받는 사람을 위한 데이터라는 것을 유추한다면, Receiver 는 그 자체로 받는 사람을 의미함.

    Value Type 을 사용함으써 개념적으로 완전한 하나를 잘 표현할 수 있다.

    ShippingInfo 의 주소 관련 데이터도 Address 로 표현할 수 있음.

    ```java
    public class Address {
    	private String address1;
    	private String address2;
    	private String zipCode;

    	public Address(String address1, String address2, String zipCode) {

    		this.address1 = address1;
    		this.address2 = address2;
    		this.zipCode = zipCode;
    	}
    	...
    }
    ```

    아래는 Value Type 을 이용해서 ShippingInfo 클래스를 다시 구현한 것이다.

    ```java
    public class ShippingInfo {
    	private Receiver receiver;
    	private Address address;
    	...
    }
    ```

    Value Type 이 두개 이상의 데이터를 가져가야 하는 것은 아니다.

    ```java
    public class OrderLine {
    	private Product product;
    	private int price;
    	private int quantity;
    	private int amount;
    ...
    ```

    OrderLine 의 price 와 amounts 는 int 타입의 숫자를 사용하고 있지만 이것이 의미하는 것은 돈이다. 돈을 의미하는 Money 타입을 만들면 된다.

    ```java
    public class Money {
    	private int value;

    	public Money(int value) {
    		this.money = money;
    	}

    	public int getValue() {
    		return this.value();
    	}
    }
    ```

    위 Money 를 사용해서 다시 작성한 OrderLine 이다.

    ```java
    public class OrderLine {
    	private Product product;
    	private Money price;
    	private int quantity;
    	private Money amounts;
    ...
    ```

    Money 타입은 돈 계산을 위한 기능을 넣을 수 있다.

    ```java
    public class Money {
    	private int value;
    	... 생성자, getValue();

    	public Money add(Money money) {
    		return new Money(this.value + money.value);
    	}

    	public Money multiply (int multiplier) {
    		return new Money(value * multiplier);
    	}
    }
    ```

    Money 를 사용하는 코드는 ‘정수 타입 연산’ 이 아니라 ‘돈 계산’ 이라는 의미로 사용할 수 있음.

    ```java
    public class OrderLine {
    	
    	private Product product;
    	private Money price;
    	private int quantity;
    	private Money amounts;

    	public OrderLine (Product product, Money price, int quantity) {
    		this.product = product;
    		this.price = price;
    		this.quantity = quantity;
    		this.amounts = calculateAmounts();
    	}

    	private Money calculateAmounts() {
    		return price.multiply(quantity);
    	}

    	public int getAmounts() {...}
    	...
    }
    ```

    Value 객체의 데이터를 변경할 때는 기존 데이터를 변경하기보다는 변경한 데이터를 갖는 새로운 Value 객체를 생성하는 방식을 선호.

    ```java
    public class Money {
    	private int value;
    	
    	public Money add(Moeny money) {
    		return new Money(this.value + money.value);
    	}
    	//value 변경할 수 있는 메서드 없음
    }
    ```

    위처럼 데이터 변경이 없는 타입을 불변이라고 표현함.

    Value Type 을 불변으로 구현하는 이유는 보다 안전한 코드를 작성할 수 있기 때문

    예로 OrderLine 을 생성하려면 다음 처럼 Money 객체를 전달해야한다.

    ```java
    Money price = ...;

    OrderLine line = new OrderLine(product, price, quantity);

    ```

    set 으로 데이터 변경이 가능해진다면 아래와 같은 일이 발생함

    ```java
    Money price = new Money(1000);
    OrderLine line = new OrderLine(product, price, 2); // price = 1000, quantity = 2, amounts = 2000
    price.setValue(2000); // price = 2000, quantity = 2, amounts = 2000
    ```

    위와 같은 일이 발생하지 않게 하려면 새로운 Money 객체를 생성하도록 코드를 작성해야함.

    ```java
    public class OrderLine {
    	...
    	private Money price;

    	public OrderLine( Product product, Moeny price, int quantity) {
    		this.product = product;
    		// Money 가 불변 객체가 아니라면,
    		// price 파라미터가 변경될 때 발생하는 문제를 방지하기 위해
    		// 데이터를 복사한 새로운 객체를 생성해야함.

    		this.price = new Money(price.getValue());
    		this.quantity = quantity;
    		this.amounts = calculateAmounts();
    	}
    }
    ```

    Money 가 불변이면 위 같은 코드는 작성할 필요가 없다. Money의 데이터를 바꿀 수 없기 때문에 파라미터로 전달받은 price 를 안전하게 사용할 수 있다.





추가-------------------------------------------------------------------------------------------------

### 엔티티 식별자와 밸류 타입

엔티티 식별자의 실제 데이터는 String 과 같은 문자열로 구성된 경우가 많음.

ex) 신용카드 번호, 회원 구분용 email 등

식별자는 특별한 의미를 지니는 경우가 많음. 그래서 식별자를 위한 밸류 타입을 사용해서 의미가 잘 드러나도록 할 수 있음.

_주문번호를 표현하기 위해 Order의 식별자 타입으로 String 대신 OrderNo 밸류 타입을 사용하면 타입을 통해 해당 필드가 주문번호라는 것을 알 수 있음._

```java
public class Order {
	// OrderNo 타입 자체로 id가 주문번호임을 알 수 있음.
	private OrderNo id;

	...

	public OrderNo getId() {
		return id;
	}
}
```

OrderNo 대신 String 타입을 사용하면 ‘id’ 라는 이름만으로는 해당 필드가 주문번호인지 여부를 알 수 없음.

#### 도메인 모델에 set 메서드 넣지 않기

get/set 메서드는 습관적으로 추가하는 메서드임..

도메인 모델에 get/set 메서드를 추가하는 것은 좋지 않다.

특히 set 메서드는 도메인의 핵심 개념이나 의도를 코드에서 사라지게 한다.

다음은 Order의 메서드를 set 메서드로 변경한 코드이다.

```java
public class Order {
	...
	public void setShippingInfo(ShippingInfo newShipping) {...}
	public void setOrderState(OrderState state) {...}
}
```

set 메서드의 문제는 도메인 객체를 생성할 때 완전한 상태가 아닐 수도 있음.

```java
Order order = new Order();

order.setOrderLine(lines);
order.setShippingInfo(shippingInfo);

order.setState(OrderState.PREPARING);
```

위 코드는 주문자가 누락되있음.

생성 시점에서 필요한 데이터를 다 받아야 됨.

```java
Order order = new Order(orderer, lines, shippingInfo, OrderState.PREPARING);
```

생성자로 데이터를 받으므로 생성자를 호출하는 시점에 필요한 데이터가 올바른지 검사할 수 있음.

```java
public class Order{
	public Order(Orderer orderer, List<OrderLine> orderLines, ShippingInfo shippingInfo,
								ShippingInfo shippingInfo, OrderSate state) {
	
		setOrderer(orderer);
		setOrderLines(orderLines);
	}

	private void setOrderer(Orderer orderer) {
		if(orderer == null) throw new IllegalArgumentException("no orderer");
		this.orderer = ordeerer;
	}
	
	private void setOrderLines(List<OrderLine> orderLines) {
		verifyAtLeastOneOrMoreOrderLines(orderLines);
		calculateTotalAmounts();
	}

	private void verifyAtLeastOneOrMoreOrderLines(List<OrderLine> orderLines) {
		if(orderLines == null || orderLines.isEmpty()) {
			throw new IllegalArgumentException("no OrderLine");
		}
	}

	private void calculateTotalAmounts() {
		this.totalAmounts = orderLines.stream().mapToInt(x -> x.getAmounts()).sum();
	}
}

```

여기서 사용하는 set 은 그냥 set이 아님. private 이기 때문에 외부에서 데이터를 변경할 목적으로 set 메서드를 사용할 수 없음.
