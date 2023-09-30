# Stream

Stream 컬렉션, 배열 등에 저장되어 있는 요소들을 하나씩 참조하며 반복적인 처리를 가능하게 하는 기능



### 특징

1. Stream 은 데이터를 변경하지 않는다. ( 데이터 저장소가 아니다)
2. Stream 은 재사용할 수 없다.
3. Stream 은 작업을 내부 반복으로 처리한다.
4. Stream 은 무제한일 수도 있다.

### 구조

Stream API 는 Stream 생성, 중개연산, 최종연산 3단계로 구분된다.

* Java 의 모든 Collection 은 내부에 stream() 이라는 메소드가 존재, 이 함수로 stream 생성 할 수 있다. Stream.of() 로도 가능
* 중개연산은 map, filter 와 같이 stream 을 return 하는 함수, stream 을 리턴함으로써, 중개연산은 메서드 체이닝이 가능하다.
* 최종연산은 중개연산과는 달리 특정 자료형을 리턴하기에 stream api 연산을 이어서 할 수 없다.



### Collect Method

중개 연산 등에 의해 가공이 끝난 stream 을 collection으로 바꾸어주는 최종연산 method

파라미터에 따라서 어떤 형태의 collection을 return 할지 정할 수 있음.

joining: stream 의 원소들을 String 으로 붙인다.

```java
Stream.of(1, 3, 3, 5, 5)
      .filter(i -> i > 2)
      .map(i -> i * 2)
      .map(i -> "#" + i)
      .collect(joining(", ", "[", "]")) // // [#6, #6, #10, #10]
```

toList(): stream의 원소들을 리스트에 담아서 반환한다.

toSet(): stream 의 원소들을 set 에 담아서 반환한다. (distinct() 라는 중개연산자를 사용하면, toList를 통해 순서를 보장할 수 있음)



### Reduce Method

Stream의 원소들을 하나씩 줄여나감.

최종연산 메소드, reduce 형태로 사용된다. 원소들을 하나의 결과로 합친다.





Stream API 를 사용함으로써 얻는 이점은 코드의 가독성이 높아지고, 실수의 여지를 줄여준다.

```java
final List<Product> products =
        Arrays.asList(
            new Product(1L, "A", new BigDecimal("100.50")),
            new Product(2L, "B", new BigDecimal("23.00")),
            new Product(3L, "C", new BigDecimal("31.45")),
            new Product(4L, "D", new BigDecimal("80.20")),
            new Product(5L, "E", new BigDecimal("7.50"))
        );

System.out.println("Total Price: " +
                   products.stream()
                   .map(product -> product.getPrice())
                   .reduce(BigDecimal.ZERO, (price1, price2) -> price1.add(price2))
                  );
```

