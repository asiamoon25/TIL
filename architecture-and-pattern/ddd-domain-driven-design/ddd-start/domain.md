# Domain 용어

코드 작성 시 도메인에서 사용하는 용어는 매우 중요.

코드 해석에 들어가는 부담을 줄여줌.

```java
public OrderState P
	STEP1, STEP2, STEP3, STEP4, STEP5, STEP6
}
```

실제 주문 상태는 ‘결제 대기중’ , ‘상품준비중’, ‘출고 완료됨’, ‘배송중’, ‘배송 완료됨’, ‘주문 취소됨’임.

하지만 개발할 때 6단계로 보고 코드로 표현한 것임.

```java
public class Order {
	public void changeShippingInfo(ShippingInfo newShippingInfo) {
		verifyStep1OrStep2();
		setShippingInfo(newShippingInfo);
	}

	public void verifyStep1OrStep2() {
		if (state != OrderState.STEP1 && state != OrderState.STEP2)
			throw new IllegalStateException("already shipped");
	}
}
```

‘출고 전’ 이라는 상태에서만 배송지 변경이 가능한데 verifiyStep1OrStep2 라는 메서드 이름은 도메인의 중요 규칙이 드러나지 않는다. 위 코드를 이해하려면 STEP1, STEP2 를 이해해야 한다.

하지만 도메인 용어를 사용해서 OrderState를 구현하면 위와 같은 변환과정을 거치지 않아도 된다.

```java
public enum OrderState {
	PAYMENT_WAITING, PREPARING, SHIPPED, DELIVERING, DELIVERY_COMPLETED;
}
```
