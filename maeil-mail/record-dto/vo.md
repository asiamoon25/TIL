# VO

## 1. VO

**VO(Value Object)** 는 **값을 표현하기 위한 객체** 로, 주로 특정 속성을 그룹화하거나 하나의 단위로 묶어서 다루기 위해 사용됨. VO 는 데이터를 저장하고, 해당 데이터를 기반으로 비교하거나 조작하는데 초점이 맞춰져 있음.

***

## 2. VO 의 주요 특징

### 1. 불변성

* VO 는 생성된 이후 그 값이 변경되지 않도록 설계됨.
* 이를 통해 VO 는 안전하고 예측 가능한 상태를 유지할 수 있음.

### 2. 동등성

* VO 의 동등성은 **값을 기준으로 판단** 됨.
  * 두 VO 객체의 값이 같다면 동일한 객체로 간주
  * 메모리 주소(참조)나 객체 ID 가 아니라, VO 가 가진 데이터(값) 로 비교.

### 3. 도메인 의미

* VO 는 특정 도메인(업무 영역) 에서 중요한 값 또는 속성을 표현함.
  * 예: 돈(`money`) , 좌표 (`Coordinates`), 이름(`Name`), 주소(`Address`)

***

## 3. VO와 POJO, DTO 비교

| **특징** | **VO (Value Object)** | **POJO (Plain Old Java Object)** | **DTO (Data Transfer Object)** |
| ------ | --------------------- | -------------------------------- | ------------------------------ |

| **목적** | 값을 표현하고 동등성 판단 | 자바의 일반적인 객체 | 데이터 전송 및 계층 간 데이터 이동 |
| ------ | -------------- | ----------- | -------------------- |

| **불변성** | 보장 | 필요에 따라 선택 | 필요 없음 |
| ------- | -- | --------- | ----- |

| **동등성 비교** | 값 기반 비교 | 참조 또는 값 | 참조 또는 값 |
| ---------- | ------- | ------- | ------- |

| **비즈니스 로직** | 없음 | 있음 | 없음 |
| ----------- | -- | -- | -- |

| **계층** | 도메인 계층 | 도메인 계층 | 데이터 전송 계층 |
| ------ | ------ | ------ | --------- |

***

## 4. VO 의 장점

### 1. 불변성&#x20;

* 데이터 일관성을 보장하며, 동시성 문제를 방지

### 2. 가독성

* 도메인에서 중요한 값을 의미 있는 방식으로 캡슐화

### 3. 코드 재사용성

* 동일한 도메인 개념을 여러 곳에서 사용할 수 있음.

### 4. 유효성 검사

* VO 생성 시 유효성 검사를 포함하여 잘못된 데이터가 들어가지 않도록 방지



***

## 예제

```java
public class Coordinates {
    private final double latitude;
    private final double longitude;

    public Coordinates(double latitude, double longitude) {
        if (latitude < -90 || latitude > 90) {
            throw new IllegalArgumentException("Latitude must be between -90 and 90.");
        }
        if (longitude < -180 || longitude > 180) {
            throw new IllegalArgumentException("Longitude must be between -180 and 180.");
        }
        this.latitude = latitude;
        this.longitude = longitude;
    }

    public double getLatitude() {
        return latitude;
    }

    public double getLongitude() {
        return longitude;
    }

    @Override
    public boolean equals(Object o) {
        if (this == o) return true;
        if (o == null || getClass() != o.getClass()) return false;
        Coordinates that = (Coordinates) o;
        return Double.compare(that.latitude, latitude) == 0 &&
                Double.compare(that.longitude, longitude) == 0;
    }

    @Override
    public int hashCode() {
        return Objects.hash(latitude, longitude);
    }

    @Override
    public String toString() {
        return "Coordinates{" +
                "latitude=" + latitude +
                ", longitude=" + longitude +
                '}';
    }
}

```
