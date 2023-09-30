# Docker-Compose

**Docker Compose**란

* 여러 컨테이너를 가지는 애플리케이션을 통합적으로 Docker 이미지를 만들고, 만들어진 각각의 컨테이너를 시작 및 중지하는 등의 작업을 더 쉽게 수행할 수 있도록 도와주는 도구

**특징**

1.  서비스들을 시작, 중지, 재빌드 한다.

    서비는 = 컨테이너에서 돌아가는 애플리케이션.

    복수의 서비스를 시작하고 중지하고 다시 빌드한다고 보면 됨.
2.  실행 중인 서비스의 상태를 볼 수 있다.

    돌아가는 docker-compose 의 로그를 볼 수 있다.
3.  실행 중인 서비스의 로그를 스트림할 수 있다.

    ```java
    docker-compose logs -f
    ```
4. 하나의 서비스에 일회성 작업을 수행할 수 있음

**Docker Compose WorkFlow**

1.  서비스 빌드

    ```java
    vi docker-compose.yml
    ```
2.  서비스 시작

    ```java
    docker-compose up --build
    ```
3.  서비스 정리

    사용 끝난 서비스 해체, 컨테이너 중지, 제거 가능.
