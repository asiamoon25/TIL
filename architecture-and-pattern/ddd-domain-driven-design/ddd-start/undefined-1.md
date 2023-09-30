# 도메인 모델 도출

도메인 모델 도출

기본 작업

1. 핵심 구성요소
2. 규칙
3. 기능

위 기본 작업은 요구사항에서 출발.

주문 도메인 관련 요구사항

1. 최소 한 종류 이상의 상품을 주문
2. 한 상품을 한 개 이상 주문 할 수 있음
3. 총 주문 금액은 각 상품의 구매 가격 합을 모두 더한 금액
4. 각 상품의 구매 가격 합은 상품 가격에 구매 개수를 곱한 값
5. 주문할 때 배송지 정보를 반드시 지정
6. 배송지 정보는 받는 사람 이름, 전화번호, 주소로 구성
7. 출고를 하면 배송지 정보를 변경할 수 없음
8. 출고 전에 주문을 취소할 수 있다.
9. 고객이 결제를 완료하기 전에는 상품을 준비하지 않는다.

위 요구 사항에서 '주문' 은 '출고 상태로 변경하기', '배송지 정보 변경하기', '주문 취소하기', '결제 완료로 변경하기' 이 4가지 기능을 제공한다는 것.

Order 관련 기능을 메서드로 추가 할 수 있음.

**Order 는 도메인 모델이고 확장 가능함**

```java
public class Order {
        public void changeShipped() {...}
        public void changeShippingInfo(ShippingInfo newShipping) {...}
        public void cancel() {...}
        public void comletePayment() {...}
}

다음 요구 사항은 주문 항목이 어떤 데이터로 구성되는지 알려줌.
* 한 상품을 한 개 이상 주문 할 수 있다.

* 각 상품의 구매 가격

두 요구 사항에 따르면 주문 항목을 표현하는 OrderLine 은 적어도 주문할 상품, 상품의 가격, 구매 개수를 포함하고 있어야함.

추가로 각 구매 항목의 구매 가격도 제공해야함.

구현한 OrderLine

```

```java
public OrderLine(Product product, int price, int quantity) {
    this.product = product;
    this.price = price;
    this.quantity = quantity;
    this.amounts = amounts;
}
private int calculateAmounts() {
    return price * quantity;
}

public int getAmounts() {...}
...

```

OrderLine 은 한 상품 을 얼마에, 몇 개 살지를 필드에 담고 있고 calculateAmounts() 메서드로 구매 가격을 구하는 로직을 구현하고 있음.

다음 요구 사항은 Order와 OrderLine 과의 관계를 알려줌

* 최소 한 종류 이상의 상품을 주문해야 한다.
* 총 주문 금액은 각 상품의 구매 가격 합을 모두 더한 금액임.

한 종류 이상의 상품을 주문할 수 있으므로 Order는 최소 한 개 이상의 OrderLine을 포함해야함. 또한, OrderLine 으로 부터 총 주문 금액을 구할 수 있음.

이 요구 사항은 Order에 다음과 같이 반영할 수 있음.

```java
public class Order {
    private List<OrderLine> orderLines;
    private int totalAmounts;

    public Order(List<OrderLine orderLines) {
        setOrderLines(orderLines);
    }

    private void setOrderLines(List<OrderLine> orderLines) {
        verifyAtLeastOneOrMoreOrderLines(orderLines);
        this.orderLines = orderLines;
        calculateAmounts();
    }

    private void verifyAtLeastOneOrMoreOrderLines(List<OrderLine> orderLines) {
        if(orderLines == null || orderLines.isEmpty()) {
            throw new IllegalArgumentException("no OrderLine");
        }
    }

    private void calculateAmounts() {
        this.totalAmounts = new Money(orderLines.stream().mapToInt(x -> x.getAmounts().getValue()).sum());
    }

    ... 다른 메서드
}

```

Order는 한 개 이상의 OrderLine 을 가질 수 있으므로 Order를 생성할 때 OrderLine 목록을 List로 전달함. 생성자를 호출하는 setOrderLines() 메서드는 요구사항에 정의한 제약 조건을 검사함. 최소 한 종류 이상의 상품을 주문해야 하므로 setOrderLines() 메서드는 verifyAtLeastOneOrMoreOrderLines() 메서드를 이용해서 OrderLine이 한 개 이상 존재하는지 검사함. 또한 calculateAmounts() 메서드를 이용해서 총 주문 금액을 계산함.

배송지 정보는 이름, 전화번호, 주소 데이터를 가지므로 ShippingInfo 클래스를 다음과 같이 정의할 수 있다.

```java
public class ShippingInfo{
    private String receiverName;
    private String receiverPhoneNumber;
    private String shippingAddress1;
    private String shippingAddress2;
    private String shippingZipcode;

    ... 생성자, getter
}
```

요구사항 중에 **주문할 때 배송지 정보를 반드시 지정해야 한다** 는 내용이 있음.

이는 Order를 생성할 때 OrderLine의 목록 뿐만 아니라 ShippingInfo 도 함께 전달해야 함을 의미함. 이를 생성자에 넣으면 됨.

```java
public class Order{
    private List<OrderLine> orderLines;
    private int totalAmounts;
    private ShippingInfo shippingInfo;

    public Order(List<OrderLine> orderLines, ShippingInfo shippingInfo) {
        setOrderLines(orderLines);
        setShippingInfo(shippingInfo);
    }

    private void setShippingInfo(ShippingInfo shippingInfo) {
        if(shippingInfo == null)
            throw new IllegalArgumentException("no ShippingInfo");
            this.shippingInfo = shippingInfo;
    }
    ...
}

```

생성자에서 호출하는 setShippingInfo() 메서드는 ShippingInfo가 null 이면 Exception 이 발생하는데, 이렇게 함으로써 '배송지 정보 필수' 라는 도메인 규칙을 구현함.

구현 중에는 특정 족너이나 상태에 따라 제약이나 규칙이 달리 적용되는 경우가 많음

* 출고를 하면 배송지 정보를 변경할 수 없다.
* 출고 전에 주문을 취소할 수 있다.

이 요구사항을 충족하려면 주문은 적어도 출고 상태를 표현할 수 있어야함.

다음 요구사항도 상태와 관련이 있음.

* 고객이 결제를 완료하기 전에는 상품을 준비하지 않는다.

위 요구 사항을 보면 결제 완료 전 상태, 결제 완료, 상품 준비 중이라는 상태가 필요한걸 알 수 있음.

```java
public enum OrderState {
	PAYMENT_WAITING, PREPARING, SHIPPED, DELIVERING, DELIVERY_COMPLETED, CANCELED;
}
```

배송지 변경이나 주문 취소 기능은 출고 전에만 가능하다는 제약 규칙이 있으므로 이 규칙을 적용하기 위해 changeShippingInfo()와 cancel() 은 verifyNotYetSHipped() 메서드를 먼저 실행함.

```java
public class Order{
    private OrderState state;

    public void changeShippingInfo(ShippingInfo newSHippingInfo) {
        verifyNotYetSHipped();
        setShippingInfo(newSHippingInfo);
    }

    public void cancel() {
        verifyNotYetSHipped();
        this.state = OrderState.CANCELED;
    }

    private void verifyNotYetSHipped() {
        if(state != OrderState.PAYMENT_WAITING && state != OrderState.PREPARING)
        throw new IllegalArgumentException("already shipped!");
    }
    ...
}
```
