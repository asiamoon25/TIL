# OpenJDK 64-Bit Server VM warning:INFO: os::com...

재시작이 답입니다.

### 원인

***

1. 시스템의 물리적 RAM 또는 스왑 공간 부족
2. 프로세스 크기 제한에 도달

### 해결방법

***

1. 시스템의 메모리 로드 줄이기
2. 물리적 메모리 또는 스왑 공간 늘리기
3. 스왑 백킹 저장소가 가득 차 있는지 확인
4. 64비트 OS 에서 64비트 JAVA 사용
5. JAVA 힙 크기 줄이기(-Xmx / -Xms)
6. JAVA 스레드 수 줄이기
7. JAVA 스레드 스택 크기 줄이기 (-Xss)
8. -XX:ReservedCodeCacheSize= 를 사용하여 더 큰 코드 캐시 설정

가장 좋은 방법은 tomcat 올라와 있는 걸 kill 로 먼저 죽인 뒤 조금 기다렸다가 startup 하는 것이 가장 좋다.
