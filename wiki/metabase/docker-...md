# 왜 Docker 로..

환경

**CentOS 6.10**

**Jar 를 설치하기 보다 Docker 를 통해 설치하기로 함.**



### **Docker 가 Jar 설치 방법보다 좋은점**

#### **1. 설치 및 실행이 간편함**

* **Jar 실행** : Metabase 를 실행하려면 **Java(JDK 11 이상)** 을 설치해야 하고, 실행 시 필요한 **환경 변수 설정** 도 필요함.
* **Docker 실행** : `docker run` 명령어 한 줄이면 바로 실행 가능

```bash
docker run -d -p 3000:3000 --name metabase metabase/metabase
```

#### 2. 환경 구성 및 배포가 쉬움

* Jar 방식 : 직접 환경 변수를 설정해야 하고, Java 실행 환경을 수동으로 관리해야함.
* Docker : `docker-compose.yml` 을 사용하면 모든 환경 변수를 쉽게 관리할 수 있음.

#### 3. 자동 재시작 및 관리가 편리함.

* Jar 방식 : 서버가 재부팅 되면 **수동으로 다시 실행해야 함**
* Docker : `restart: always` 옵션을 사용하면 컨테이너가 자동으로 다시 시작됨.

#### 3. 확장성과 배포가 용이함.

* Jar 방식 :  수동으로 여러 개 실행해야 함.
* Docker : k8s, Docker Swarm 등을 이용하여 쉽게 확장 가능
