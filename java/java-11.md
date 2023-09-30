# JAVA 11

## JAVA 11

#### **JAVA 8 이후 첫 LTS**

***

### 새롭게 추가된 메소드

### String Class Method

#### isBlank()

참고 : [https://www.educative.io/answers/what-is-stringisblank-in-java](https://www.educative.io/answers/what-is-stringisblank-in-java)

문자열이 null 이거나 비어있거나 공백이 있는 경우

```java
String s = "";   ==> true
String s2 = " "; ==> true
String s3 = "h i "; ==> false
String s4 = null; ==> true
----------------------------------------
public class Main {

	public static void main(String[] args) {
		String s = "";   
		String s2 = " "; 
		String s3 = "h i ";
		String s4 = null;

		System.out.println(s.isBlank());
		System.out.println(s2.isBlank());
		System.out.println(s3.isBlank());
		System.out.println(s4.isBlank());
	}
}
---------------------------------

true
true
false
true
```

**isEmpty() 와 다른 점**

공백을 체크하는 부분이 다르다.

```java
String s = " ";

System.out.println(s.isBlank());
Syetem.out.pringln(s.isEmpty());

-------------------------------

true
false
```

isBlank() 는 trim() 을 해서 공백을 없앤 뒤 글자수를 체크

isEmpty()는 trim()을 안하고 공백을 인식해서 글자수를 체크

#### lines()

참고 : [https://howtodoinjava.com/java11/string-to-stream-of-lines/](https://howtodoinjava.com/java11/string-to-stream-of-lines/)

문자열을 라인 단위로 쪼개는 스트림을 반환

```java
String s = "This is\\n String";

Stream<String> stream = s.lines();
stream.forEach(System.out::println);

System.out.println(s.lines().count());

----------------------------------------

This is
 String

3
```

#### strip()

문자열의 앞뒤의 공백을 제거

```java
public class Main {

	public static void main(String[] args) {
		String s = " Hi A ";
		
		String stripStr = s.strip();
		
		System.out.println(s);
		System.out.println(stripStr);
	}
}

-----------------------------------------------

 Hi A 
Hi A
```

**trim() 과의 차이**

*   trim()

    ASCII 값이 32(’U+0020’ 또는 공백) 보다 작거나 같은 모든 선행 후행 문자를 제거 한다.

    하지만 유니코드 표준에 따르면 ASCII 값이 U+0020 이상인 다양한 공백 문자가 있다.

    ex) U+2001

    이렇게 해서 나온게 JAVA 1.5의 Character 클래스에 새로운 메소드 isWhitespace(int) 가 추가되었다.
*   strip()

    Character.isWhitespace(int) 메소드를 사용해서 광범위한 공백 문자를 처리하고 제거한다.

    ```java
    public class StringTrimVsStripTest {
        public static void main(String[] args) {
            String string = '\\u2001'+"String    with    space"+ '\\u2001';
            System.out.println("Before: \\"" + string+"\\"");
            System.out.println("After trim: \\"" + string.trim()+"\\"");
            System.out.println("After strip: \\"" + string.strip()+"\\"");
       }
    }
    ```

    ```java
    Before: "  String    with    space  "
    After trim: " String    with    space "
    After strip: "String    with    space"
    ```

#### stripLeading()

문자열의 앞의 공백을 제거

```java
System.out.println(" PARK ".stripLeading());

PARK_    <==공백은_로 표시했음
```

#### stripTrailing()

문자열의 뒤의 공백을 제거

```java
System.out.println(" PARK ".stripTrailing());

_PARK  <==공백은_로 표시했음
```

#### repeat()

repeat() 은 지정한 숫자만큼 문자열을 반복해서 더한다.

```java
System.out.println("S".repeat(10)); 

SSSSSSSSSS
```

### Collection

#### toArray()

기존 stream 으로 받아서 하던 작업을 Collection으로 처리 가능

```java
public class Main{

	public static void main(String[] args){
		//JAVA 8
		List<String> list = Array.asList("Doc","Grumpy","Happy",
				"Sleepy","Dopey","Bashful","Sneezy");
		
		String[] array = list.stream().toArray(String[]::new);

		// JAVA 11
		String[] array1 = list.toArray(String[]::new);
	}
}

//JAVA 11

```
