# 문제점

MS 직원이 mysqlsh 로 하면 dump 중 talbe lock 이 발생하지 않는다고 하여 테스트해본 결과



<figure><img src="../../../../.gitbook/assets/image (34).png" alt=""><figcaption></figcaption></figure>

원래라면 0.40 정도 지연이 있던 것과는 다르게 8.80까지 올라간 것을 볼 수 있다.



lock 에 걸린 것은 아니여서 해당 부분은 모니터링을 하면서 체크해봐야 하는 부분이다.
