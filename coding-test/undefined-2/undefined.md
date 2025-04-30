# 회전 초밥

{% embed url="https://www.acmicpc.net/problem/2531" %}

### 문제 요약

회전 초밥 벨트에서 연속으로 K 개의 접시를 먹을 때, 중복되지 않는 초밥 종류의 최대 개수를 구하되, 쿠폰으로 제공되는 초밥(c번 초밥)을 하나 더 먹을 수 있다면 포함시킨다. 벨트는 원형이므로 끝에서 시작으로 이어진다.

***

### 적용 알고리즘

이 문제는 **연속된 구간의 정보를 빠르게 갱신** 해야 하므로 **슬라이딩 윈도우** 가 적합하다.

특정 범위의 초밥 종류 수를 **효율 적으로 관리하기 위해** `Map`  또는 `배열` **을 이용한 카운팅** 을 사용한다.

***

### Java 코드

```java
import java.util.*;

public class Main {
    public static void main(String[] args) {
        Scanner sc = new Scanner(System.in);
        
        int N = sc.nectInt(); // 접시 수
        int d = sc.nextInt(); // 초밥 가짓수
        int k = sc.nextInt(); // 연속으로 먹는 접시 수
        int c = sc.nextInt(); // 쿠폰 번호
        
        int[] arr = new int[N]; // 초밥 벨트
        for(int i = 0; i < N; i++) {
            arr[i] = sc.nextInt();
        }
        
        int[] count = new int[d + 1]; // 각 초밥 종류의 먹은 개수 카운트
        int unique = 0;
        
        //초기 윈도우 설정
        for(int i = 0; i < k; i++) {
            int sushi = arr[i % N];
            if(count[sushi] == 0) unique++; // 처음 먹는 종류묜 증가
            count[sushi]++;
        }
        
        // 쿠폰 초밥 처리
        if(count[c] == 0) unique++;
        
        int max = unique;
        
        //슬라이딩 윈도우 : 총 N 번만 이동해보면 충분(원형 구조 고려)
        for(int i = 1; i < N; i++) {
            //윈도우 맨 앞 제거
            int remove = arr[(i - 1) % N];
            count[remove]--;
            if(count[remove] == 0 && remove != c) unique--; // 종류 빠지면 감소
            
            // 윈도우 새로 추가
            int add = arr[(i + k - 1) % N];
            if(count[add] == 0) unique++;
            count[add]++;
            
            // 쿠폰 초밥이 이미 포함되어 있지 않다면 보너스로 +1
            int current = unique;
            if(count[c] == 0) current++;
            
            max = Math.max(max, current);
            
            max = Math.max(max, current);
        }
        
        System.out.println(max);
    }
}
```

***

### 요약

1. 슬라이딩 윈도우를 이용해 연속 구간을 탐색
2. 초밥 중복을 count 배열로 관리
3. 쿠폰 초밥은 항상 포함 여부를 확인하여 + 1 처리
4. 원형 벡트를 인덱스 `%N`  으로 처리하여 회전 구현
