# 조건&반복문

### Java 의 조건문 & 반복문

Java 에서 **조건문(Conditional Statements)** 과 **반복문(Loop Statements)** 은 프로그램의 흐름을 제어하는 중요한 문법임.

이를 활용하면 **특정 조건에 따라 코드 실행 여부를 결정** 하거나, **반복적으로 실행해야 하는 코드 블록을 관리** 할 수 있음.

***

### 1. 조건문(Conditional Statements)

조건문은 **주어진 조건을 평가하여 코드 실행을 결정하는 문법**

Java 에는 `if` , `if-else` , `else-if` , `switch` 가 있음.

#### 1.1 if 문

`if` 문은 조건이 `참(true)` 일 때만 실행됨.

```java
if ( 조건식 ) {
    // 조건이 true 일 때 실행되는 코드
}
```

#### 1.2 if-else 문

`if` 문과 함께 `else` 문을 사용하면 **조건이 거짓(false) 일 때** 실행할 코드도 지정할 수 있음.

```java
if ( 조건식 ){
    // 조건이 true 일 때 실행
} else {
    // 조건이 false 일 때 실행
}
```

#### 1.3 else-if 문

여러 조건을 검사하려면 `else if` 문을 사용하면 됨.

```java
if( 조건식1 ) {
    // 조건식1이 true 일 때 실행
} else if ( 조건식2 ) {
    // 조건식1이 false 이고, 조건식2가 true 일 때 실행
} else {
    // 모든 조건이 false 일 때 실행
}
```

#### 1.4 switch 문

`switch` 문은 **한 변수의 값에 따라 여러 경우(case) 중 하나를 실행** 할 때 사용함.

```java
switch( 변수 ) {
    case 값1:
        // 값1 일 때 실행할 코드
        break;
    case 값2:
        // 값2 일 때 실행할 코드
        break;
    default:
        // 모든 case가 아닐 때 실행할 코드
}
```

***

### 2. 반복문(Loop Statements)

반복문은 **조건이 만족될 때까지 같은 코드를 여러 번 실행하는 문법**

Java 에는 `for` , `while` , `do-while` 문이 있음.&#x20;

#### 2.1 for 문&#x20;

반복 횟수가 정해져 있을 때 사용하는 반복문

```java
for ( 초기식; 조건식; 증감식 ) {
    // 반복 실행할 코드
}
```

#### 2.2 while 문

```java
while( 조건식 ) {
    // 조건이 true 일 때 반복 실행
}
```

#### 2.3 do-while 문

`do-while` 문은 **최소 한 번 실행한 후 조건을 검사** 하는 반복문

```java
do{
    //실행할 코드
} while( 조건식 );
```

***

### 3. 반복문 제어(break & continue)

#### 3.1 break 문

`break` 문은 **반복문을 즉시 종료** 할 때 사용

```java
for(int i = 1; i <= 10; i++) {
    if(i == 5) {
        break; // 5에서 반복문 종료
    }
    System.out.println(i);
}
```

출력

```
1
2
3
4
```

#### 3.2 continue 문

`continue` 문은 **현재 반복을 건너뛰고 다음 반복으로 이동**

```java
for(int i = 1; i <= 5; i++) {
    if ( i == 3 ) {
        continue;
    }
    System.out.println(i);
}
```

출력

```
1
2
4
5
```

***

### 4. 향상된 for문(Enhanced for loop)

배열이나 리스트를 순회할 때 사용

```java
for ( 데이터타입 변수명 : 배열 ) {
    // 반복 실행할 코드
}
```

