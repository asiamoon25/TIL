# Java 예외처리

## 예외(Exception) 의 기본 개념 및 종류

### 예외의 정의와 동작 방식

**예외(Exception)** 란 프로그램 실행 중이 발생하는 **비정상적인 상황** 또는 **에러 조건** 을 나타내는 객체임. 자바에서는 이러한 예외가 발생하면 **프로그램의 정상 흐름이 중단** 되고 해당 예외를 처리할 수 있는 코드를 찾아 실행 흐름을 변경함. 예외가 발생했는데도 적절히 처리하지 않으면 프로그램은 곧바로 **비정상 종료** 되며, 이를 방지하기 위해 **try-catch** 구문 등을 사용해 예외를 처리해야함. 예외 처리는 발생한 오류 상황을 잡아내서 프로그램이 완전히 종료되지 않고 **안정적으로 마무리** 될 수 있도록 하는 기법임.

예외가 발생하면 **호출스택(call stack)** 을 따라 발생 지점에서 상위 메서드로 전파(propagation) 됨. 즉, 메서드에서 예외 처리되지 않으면 그 메서드를 호출한 상위 메서드로 예외가 던져지고, 또 거기서 처리되지 않으면 그 상위로 계속 전달됨. 이러한 전파 과정에서 해당 예외를 처리할 수 있는 첫 번째 **catch 블록** 이 나타나면 예외는 잡혀서 처리되고, 그렇지 않으면 최종적으로 프로그램이 종료됨.

자바에서는 **try-catch-finally** 구문으로 예외를 처리함. `try` 블록 안에 예외 발생 가능성이 있는 코드를 넣고, `catch` 블록에서 발생한 예외 타입별로 처리 코드를 작성함. 필요한 경우 `finally` 블록을 사용하여 예외 발생 여부와 관계없이 **항상 실행** 해야 하는 정리 코드를 넣을 수 있음.&#x20;

```java
System.out.println("프로그램 시작");
try {
    int result = 10 / 0;  // 0으로 나누면 ArithmeticException 발생
    System.out.println("연산 결과: " + result);  // 이 코드는 실행되지 않음
} catch (ArithmeticException e) {
    System.out.println("예외 발생: " + e.getMessage());
} finally {
    System.out.println("finally 블록 실행: 자원 정리 등");
}
System.out.println("프로그램 종료");
```

위 코드에서 `try` 블록 내부에서 **예외가 발생하면** 즉시 실행이 중지단되고 해당 예외를 처리할 수 있는 `catch` 블록으로 제어가 넘어감. `catch` 블록에서는 발생한 예외 객체(`ArithmeticException` ) 를 잡아 로그를 남기거나 복구 작업을 수행함. 마지막으로 `finally` 블록이 존재하면 예외 발생 여부와 상관없이 실행되어, 열린 자원을 닫는 등의 마무리 작업을 함. 만약 `catch` 로 잡히지 않은 예외가 메서드 밖으로 전파되면, 호출한 쪽에서도 역시 예외 처리 규칙에 따라 처리하거나 더 상위로 전달됨.

### 예외의 종류: Checked Exception vs Unchecked Exception

자바의 예외는 크게 **Checked 예외** 와 **Unchecked 예외(Runtime Exception)** 로 구분됨.

또한 `Exception` 과는 별도로 심각한 오류를 나타내는 `Error` 계열이 존재함. 이 세 가지의 차이점을 이해하는 것이 중요함.

* **Checked Exception** : 일반적인 예외로, **컴파일 시점에 처리 여부를 검사** 하는 예외임. 이런 예외가 발생할 수 있는 코드는 컴파일러가 **반드시 예외를 처리** 하거나 메서드에 선언하도록 강제함. 예를 들어 `IOException` , `SQLException` , `ClassNotFoundException` 등이 여기에 해당하며, 파일 입출력이나 DB 접근처럼 **외부 환경에 의해 발생할 수 있는 문제** 들을 나타냄. Checked 예외는 코드상에서 `try-catch` 로 처리하거나 메서드 시그니처에 `throws 예외명` 으로 명시해야함.

```java
import java.io.*;
public void readFile(String path) {
    try{
        FileReader reader = new FileReader(path); // FileNotFoundException (Checked)
        // ...파일 읽기 처리
    }catch(FileNotFoundException e) {
        // 예외 처리 : 파일을 찾을 수 없을 떄의 대응 로직
        System.out.println("파일을 찾기 못했습니다: " + e.getMessage());
    }
}
```

위 코드에서 `FileReader` 생성자는 파일이 존재하지 않을 경우 `FileNotFoundException` 이라는 Checked 예외를 발생시킴. 따라서 컴파일러는 이 예외를 처리하도록 요구하며, `try-catch` 로 잡아서 처리하거나 메서드에 `throws FileNotFoundException` 을 선언해야함. Checked 예외를 처리하지 않고 넘어가려 하면 컴파일 에러가 발생함. 즉, **Checked 예외는 반드시 처리하거나 선언해야하는 예외** 임.

* **Unchecked Exception** : **런타임 예외** 라고도 하며, `RuntimeException` 을 상속하는 예외 클래스들임. 이러한 예외는 **컴파일러가 예외 처리 여부를 검사하지 않기 때문에** ,개발자가 명시적으로 처리하지 않아도 컴파일 에러가 나지 않음. 주로 프로그래머의 실수로 발생하는 **논리 오류나 프로그램 버그** 를 나타내며, 실행 중에 발견됨. 예를 들어 `NullPointException` ,`ArrayIndexOutOfBoundsException` ,`ArithmeticException` (0으로 나누기 오류) 등이 있으며, 보통 **코드의 사전 검증으로 예방이 가능한 문제들** 임. Unchecked 예외는 선택적으로 `try-catch` 로 잡을 수 있지만, 일반적으로는 발생하지 않도록 코드를 작성해야 하며, 발생한다면 **프로그램을 수정해야 하는 버그** 로 간주함.

```java
String text = null;
System.out.println(text.length()); // NPE 발생(Unchecked)
```

