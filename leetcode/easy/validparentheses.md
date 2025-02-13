# ValidParentheses

{% embed url="https://leetcode.com/problems/valid-parentheses/" %}



```java
public static boolean isValid(String s) {

        Stack<Character> stack = new Stack<>();
        char[] arr = s.toCharArray();

        for(int i = 0; i < arr.length; i++){
            if(')' == arr[i] || '}' == arr[i] || ']' == arr[i]) {
                if(!stack.isEmpty()) {
                    char a = stack.pop();
                    int num = arr[i] - a;
                    if (num != 1 && num != 2) {
                        stack.push(arr[i]);
                    }
                } else {
                    stack.push(arr[i]);
                }
            }else {
                stack.push(arr[i]);
            }
        }

        return stack.isEmpty();
    }
```

```java
Stack<Character> stack = new Stack<>();
char[] arr = s.toCharArray();

// Stack -> 괄호 검증용
// char[] -> s를 쪼갠 배열
```

닫는 괄호일 때 로직 수행

```java
if(')' == arr[i] || '}' == arr[i] || ']' == arr[i]) {
    if(!stack.isEmpty()) { // stack 이 비어있지 않을 때
        char a = stack.pop();
        int num = arr[i] - a; // 1 또는 2 차이가 남.(아스키 코드...)
        if (num != 1 && num != 2) {
          stack.push(arr[i]); // 1또는 2차이가 아닐 때 -> 서로 다른 괄호일 때 다시 넣음
        }
    } else { // 스택이 비어있으면 스택에 처음걸 넣음.
        stack.push(arr[i]);
    }
}else { // 열린 괄호 일 때 스택에 추가
    stack.push(arr[i]);
}
```

for 문이 다 돈 후 stack 에 남아있는 값이 있으면 false, 배열이 비어 있으면 true 를 반환
