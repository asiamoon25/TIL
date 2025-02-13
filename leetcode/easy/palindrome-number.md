# Palindrome Number

{% embed url="https://leetcode.com/problems/palindrome-number/submissions/1522757057/" %}

정수 X 가 주어졌을 때, 정수 X 가 팰린드롬이면 True, 아니면 False 를 반환하시오.



문제 하단에 보면 정수를 String 으로 컨버팅 하지 않고 풀라는 말이 있다.

***

### Palindrom

* 팰린드롬 이란 숫자나 문자열을 **앞에서 읽든, 뒤에서 읽든 동일한 경우** 를 말함.
* 예를 들어:
  * 숫자 `121`-> 뒤집어도 `121` 이므로 팰린드롬임.
  * 숫자 `10` -> 뒤집으면 `01` 이므로 팰린드롬이 아님.

***

### 문제 발생

* 팰린드롬을 확인하는 알고리즘을 작성했을 때, 숫자 `10` 처럼 팰린드롬이 아닌 숫자가 **잘못된 조건** 으로 인해 팰린드롬으로 판정되는 문제가 발생했었음.

```java
public boolean isPalindrome(int x) {
        
        int reversedHalf = 0;

        while(x > reversedHalf) {
            int lastDigit = x % 10;
            reversedHalf = reversedHalf * 10 + lastDigit;
            x /= 10;
        }

        return x == reversedHalf || x == reversedHalf /10;
    }
```

위 코드에서는 10일 때 True 를 반환함.



**이전 코드 흐름**

1. 숫자를 뒤집은 절반(`reversedHalf` )를 생성함.
2. 원래 숫자의 앞부분(`x` )과 뒤집힌 절반을 비교하여 팰린드롬 여부를 판단함.
3. 짝수 자리 숫자는 `num == reversedHalf` 로 비교하고, 홀수 자리 숫자는 가운데 숫자를 무시하고 비교(`num == reversedHalf / 10` ) 함.

**수정 완료한 코드**

```java
 public boolean isPalindrome(int x) {

        if(x < 0 || (x % 10 == 0 && x != 0)){
            return false;
        }
        
        int reversedHalf = 0;

        while(x > reversedHalf) {
            int lastDigit = x % 10;
            reversedHalf = reversedHalf * 10 + lastDigit;
            x /= 10;
        }

        return x == reversedHalf || x == reversedHalf /10;
    }
```

