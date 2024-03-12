# GORM 과 JPA의 차이점

#### GORM

* Grails 특화 : GORM 은 Grails 프레임워크의 일부, Groovy 언어의 동적 기능을 활용하여 데이터베이스 작업을 단순화함. Grails 를 위해서만 설계되었음.
* 동적 기능 : GORM 은 groovy 의 동적 프로그래밍 기능을 사용하여, 런타임에 CRUD 작업을 위한 메서드를 자동으로 생성함. -> 직관적이고 간결한 코드로 DB 작업을 할 수 있게 함.
* 다양한 백엔드 지원 : GORM은 Hibernate를 기반으로 하지만, SQL, NoSQL, REST API 등 다양한 데이터 스토어를 지원함.

**Grails 특화**

아무 곳에서 쓸 수 없음. Grails 에서만 사용 가능

**동적 기능**

Groovy 는 JVM 위에서 실행되는 동적언어. Groovy 의 동적 타이핑(dynamic typing) 기능은 런타임에 객체의 타입을 결정하고, 메서드 호출 및 속성 접근과 같은 작업을 동적으로 해석함.

* \***GORM 의 CRUD 메서드 자동 생성** GORM 은 Groovy 의 동적 기능을 사용해서, 엔티티 클래스에 대한 CRUD 작업을 위한 메서드를 런타임에 자동으로 생성함. 예를 들어서 개발자가 엔티티 클래스 Book 을 생성하면 GORM 은 Book 인스턴스를 save, get, findAll, update, delete 하기 위한 메서드를 자동으로 해당 클래스에 추가함. ➡️ 메타클래스를 활용하여 처리함...
* **동적 메서드 해석** GORM 은 데이터 액세스 로직을 실행할 때 Groovy 의 동적 메서드 해석 기능을 사용하여 적절한 SQL 쿼리를 생성하고 실행함. -> Book.findByTitle("Groovy Program") 같은 호출이 있을 때 GORM 은 이를 분석해서 title 필드가 Groovy Program 인 Book 엔티티를 조회하는 SQL 을 생성하고 실행함.

**다양한 백엔드 지원**

Hibernate를 기반으로 하지만, SQL, NoSQL, REST API 등 다양한 데이터 스토어를 지원함.

#### JPA (Java Persistence API)

* 자바 표준 : Java EE 의 공식 ORM 기술, 다양한 Java 애플리케이션에서 사용됨. JPA는 ORM 표준 인터페이스를 제공하며, Hibernate, EclipseLink, OpenJPA 등 다양한 구현체를 사용할 수 있음.
* 포터빌리티 : JPA 는 특정 구현체에 종속되지 않는 표준화된 API 를 제공함. 어플리케이션을 다른 JPA 구현으로 쉽게 이전할 수 있게 함.
* 정적 타입 : JPA 는 주로 Java 언어의 정적 타입 특성을 활용함. 엔티티 클래스를 정의하여 DB 테이블과 매핑하며, JPQL을 사용하여 쿼리를 작성함.

#### 차이점

* 프로그래밍 언어와 프레임워크 : GORM 은 grails 와 Groovy 에 특화 되있으며, 동적 프로그래밍 기능을 적극 활용함. JPA 는 Java 표준이며, 다양한 Java 기반 애플리케이션에서 사용됨.
* 접근 방식 : GORM 은 런타임에 동적으로 메서드를 생성하여 개발자의 작업을 간소화하는 반면에 JPA 는 엔티티 매핑과 JPQL 을 통해 좀 더 전통적인 ORM 접근 방식을 취한다.
* 다양성과 확장성 : JPA는 그 자체로는 특정 구현체에 대한 표준 인터페이스를 제공하는 반면에 GORM 은 Grails 내에서 더 풍부하고 다양한 데이터 스토어를 직관적으로 다룰 수 있는 특화된 기능을 제공함.
