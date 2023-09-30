# Date/Time API

### 기존  Date, Calendar 문제점

*   **스레드 안정성** : Date 및 Calendar 클래스는 스레드로부터 안전하지 않다.&#x20;

    mutable 하기 때문에 다른 Thread 에서 값을 참조하고 변경할 수 있다.
* **API 디자인 및 이해 용이성** : Date 및 Calendar API 는 일상적인 작업을 수행하기에는 부적절한 방법으로 제대로 설계되지 않았다. 다양한 유틸리티 메서드가 없다.
* **ZonedDate 및 시간** : 기존 API 를 사용하여 시간대 로직을 처리하기 위해 추가로 로직을 작성해야함.



### 추가된 내용

#### LocalDate, LocalTime 및 LocalDateTime 사용

현지 날짜/시간을 나타냄.



ISO 형식의 Date를 나타냄

```
LocalDate localDate = LocalDate.now();
```

_그리고 of_ 메소드나 _구문 분석_ 메소드 를 사용하여 특정 일, 월, 연도를 나타내는 _LocalDate를_ 얻을 수 있다.

다음 코드 조각은 2015년 2월 20일의 _LocalDate를 나타냄._

```java
LocalDate.of(2015, 02, 20);

LocalDate.parse("2015-02-20");복사
```



### LocalDate

LocalDate _는_ 다양한 정보를 얻기 위한 다양한 유틸리티 방법을 제공한다.

```java
//날짜 +
LocalDate tomorrow = LocalDate.now().plusDays(1);

//날짜 -
LocalDate previousMonthSameDay = LocalDate.now().minus(1, ChronoUnit.MONTHS);


//요일과 날짜 추출
DayOfWeek sunday = LocalDate.parse("2016-06-12").getDayOfWeek();
int twelve = LocalDate.parse("2016-06-12").getDayOfMonth();

//윤년 check
boolean leapYear = LocalDate.now().isLeapYear();

// 기준날짜가 파라미터보다 미래,과거 인지check
boolean notBefore = LocalDate.parse("2016-06-12")
  .isBefore(LocalDate.parse("2016-06-11"));
boolean isAfter = LocalDate.parse("2016-06-12")
  .isAfter(LocalDate.parse("2016-06-11"));
  
//지정된 날짜의 하루의 시작을 나타내는 LocalDateTime(2016-06-12T00:00)과 해당 월의 시작을 나타내는 LocalDate(2016-06-01)를 각각 얻음
LocalDateTime beginningOfDay = LocalDate.parse("2016-06-12").atStartOfDay();
LocalDate firstDayOfMonth = LocalDate.parse("2016-06-12")
  .with(TemporalAdjusters.firstDayOfMonth());
```





### LocalTime

날짜 없이 시간을 나타냄

LocalDate 와 유사하게 인스턴스 생성 가능

```
LocalTime now = LocalTime.now();
```

문자열 -> 시간 변환

```java
LocalTime sixThirty = LocalTime.parse("06:30");
```

팩토리 메서드 사용

```java
LocalTime sixThirty = LocalTime.of(6, 30);
```

시간 더하기

```java
LocalTime sevenThirty = LocalTime.parse("06:30").plus(1, ChronoUnit.HOURS);
```

시, 분, 초 가져오기

```java
int six = LocalTime.parse("06:30").getHour();
```

특정 시간이 다른 특정 시간 이전인지 이후인지 확인할 수도 있다.

```java
boolean isbefore = LocalTime.parse("06:30").isBefore(LocalTime.parse("07:30"));
```

마지막으로 하루의 최대, 최소, 정오 시간은 _LocalTime_ 클래스의 상수로 얻을 수 있다. 이는 주어진 시간 범위 내에서 레코드를 찾기 위해 데이터베이스 쿼리를 수행할 때 매우 유용하다.

예를 들어 아래 코드는 23:59:59.99를 나타낸다.

```java
LocalTime maxTime = LocalTime.MAX
```

### LocalDateTime

날짜와 시간의 조합을 나태날 때 사용.

_LocalDateTime_ 의 인스턴스는 _LocalDate_ 및 _LocalTime_ 과 유사한 시스템 시계에서 얻을 수 있다.

```java
LocalDateTime.now();
```

&#x20;of 및 parse 메서드를 사용하여 인스턴스를 생성하는 방법

```java
LocalDateTime.of(2015, Month.FEBRUARY, 20, 06, 30);
```

```java
LocalDateTime.parse("2015-02-20T06:30:00");
```



일, 월, 연도, 분과 같은 특정 시간 단위의 더하기 및 빼기를 지원하는 유틸리티 API가 있다.

```java
localDateTime.plusDays(1);
```

```java
localDateTime.minusHours(2);
```

날짜 및 시간 클래스와 유사한 특정 단위를 추출하기 위해 Getter 메소드를 사용할 수도 있다. _위의 LocalDateTime_ 인스턴스가 주어지면 이 코드 샘플은 2월을 반환한다.

```java
localDateTime.getMonth();
```



### ZonedDateTime

Java8 은 시간대별 날짜와 시간을 처리해야할 때 ZoneDateTime 을 제공한다. ZoneId 는 서로 다른 시간대를 나타내는 데 사용되는 식별자로, 약 40개의 다른 시간대가 있음.



Paris ZoneId 얻기

```java
ZoneId zoneId = ZoneId.of("Europe/Paris");
```

그리고 모든 영역 ID 집합을 얻을 수 있다.

```java
Set<String> allZoneIds = ZoneId.getAvailableZoneIds();
```

_LocalDateTime_ 은 특정 영역으로 변환될 수 있다.

```java
ZonedDateTime zonedDateTime = ZonedDateTime.of(localDateTime, zoneId);
```

ZonedDateTime은 _시간_ 대별 날짜-시간을 가져오는 _구문 분석_ 방법을 제공한다 .

```java
ZonedDateTime.parse("2015-05-03T10:15:30+01:00[Europe/Paris]");
```

시간대를 사용하는 또 다른 방법은 _OffsetDateTime 을_ 사용하는 것. OffsetDateTime은 _오프셋_ 이 있는 날짜-시간의 불변 표현. 이 클래스는 UTC/그리니치로부터의 오프셋뿐만 아니라 모든 날짜 및 시간 필드를 나노초 단위의 정밀도로 저장한다.

OffSetDateTime 인스턴스는 _ZoneOffset 을 사용 하여_ 생성할 수 있다 . 여기서는 2015년 2월 20일 오전 6시 30분을 나타내는 _LocalDateTime을_ 만든다 .

```java
LocalDateTime localDateTime = LocalDateTime.of(2015, Month.FEBRUARY, 20, 06, 30);
```

_그런 다음 ZoneOffset을_ 생성 하고 _localDateTime_ 인스턴스를 설정 하여 시간에 2시간을 추가한다 .

```java
ZoneOffset offset = ZoneOffset.of("+02:00");

OffsetDateTime offSetByTwo = OffsetDateTime
  .of(localDateTime, offset);
```



### Period, Duration

Period 는  연, 월, 일 단위로 시간을 나타내고 Duration 클래스는 초, 나노초 단위로 시간을 나타냄



#### Period

Period 클래스는 주어진 날짜의 값을 수정하거나 두 날짜 간의 차이를 얻는데 사용

```java
LocalDate initialDate = LocalDate.parse("2007-05-10");
```

_Period를_ 사용하여 _날짜를_ 조작할 수 있다.

```java
LocalDate finalDate = initialDate.plus(Period.ofDays(5));
```



Period 클래스에는 _Period_ 개체 에서 값을 가져오는 _getYears_ , _getMonths_ 및 _getDays 와_ 같은 다양한 getter 메서드가 있다 .

예를 들어, 일수에 따른 차이를 얻으려고 시도할 때 _int 값 5를 반환한다 ._

```java
int five = Period.between(initialDate, finalDate).getDays();
```

_ChronoUnit.between을_ 사용하여 일, 월, 연도와 같은 특정 단위로 두 날짜 사이의 _기간을_ 얻을 수 있다 .

```java
long five = ChronoUnit.DAYS.between(initialDate, finalDate);
```

이 코드 예제는 5일을 반환한다.



#### Duration

_Period_ 와 유사하게 Duration _클래스는_ 시간을 처리하는 데 사용된다 _._

_초 추가_

```java
LocalTime initialTime = LocalTime.of(6, 30, 0);

LocalTime finalTime = initialTime.plus(Duration.ofSeconds(30));
```

두 순간 사이의 _기간을 기간_ 또는 특정 단위 로 얻을 수 있다 .

_먼저 Duration 클래스의 between()_ 메서드를 사용하여 _finalTime_ 과 _initialTime_ 사이의 시간 차이를 찾고 그 차이를 초 단위로 반환한다.

```java
long thirty = Duration.between(initialTime, finalTime).getSeconds();
```

_ChronoUnit_ 클래스 의 _between()_ 메서드를 사용하여 동일한 작업을 수행한다.

```java
long thirty = ChronoUnit.SECONDS.between(initialTime, finalTime);
```



### 날짜 및 달력 호환성

기존 날짜 및 달력 인스턴스를 새로운 날짜 및 시간 API 로 변환하는데 도움이 되는 toInstanc() 가 추가 됨.



```java
LocalDateTime.ofInstance(date.toInstance(), ZoneId.systemDefault());
LocalDateTime.ofInstance(calendar.toInstance(), ZoneId.systemDefault());
```



2016-06-13T11:34:50을 나타내는 _LocalDateTime_

<pre class="language-java"><code class="lang-java"><strong>LocalDateTime.ofEpochSecond(1465817690, 0, ZoneOffset.UTC);
</strong></code></pre>



### 날짜 및 시간 형식

Java 8 은 날짜 및 시간 형식을 쉽게 지정할 수 있는 API 를 제공&#x20;



```java
LocalDateTime localDateTime = LocalDateTime.of(2015, Month.JANUARY, 25, 6, 30);
```

이 코드는 ISO 날짜 형식을 전달하여 현지 날짜 형식을 지정하며 결과는 2015-01-25이다.

```java
String localDateString = localDateTime.format(DateTimeFormatter.ISO_DATE);
```

DateTimeFormatter는 _다양한_ 표준 형식 지정 옵션을 제공한다.

사용자 정의 패턴을 형식 메소드에 제공할 수도 있다. 여기서는 _LocalDate를_ 2015/01/25로 반환한다.

```java
localDateTime.format(DateTimeFormatter.ofPattern("yyyy/MM/dd"));
```

형식 지정 옵션의 일부로 _SHORT_ , _LONG_ 또는 _MEDIUM_ 형식 지정 스타일을 전달할 수 있다 .

예를 들어, 이는 2015년 1월 25일, 06:30:00의 _LocalDateTime을_ 나타내는 출력을 제공한한다 .

```java
localDateTime
  .format(DateTimeFormatter.ofLocalizedDateTime(FormatStyle.MEDIUM)
  .withLocale(Locale.UK));
```

