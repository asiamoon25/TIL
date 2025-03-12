# H2 에서 MySQL 로 Migration

## 버전 확인

![image.png](https://dev.azure.com/jeffkang/c7fa7cf4-3646-4a12-922d-4a43cae15861/_apis/git/repositories/f57dcf48-f623-4e6c-802a-06f899f38955/Items?path=/.attachments/image-7bbefc52-146c-426b-ae71-0a57ef2240e0.png\&download=false\&resolveLfs=true&%24format=octetStream\&api-version=5.0-preview.1\&sanitize=true\&versionDescriptor.version=wikiMaster)\
v0.46.4 확인

## metabase 홈페이지에서 jar 파일 설치(동일한 버전으로)

[https://downloads.metabase.com/v0.46.4/metabase.jar](https://downloads.metabase.com/v0.46.4/metabase.jar)&#x20;

## 로컬 PC에서 준비한 MySQL 접속 되는지 확인(툴 or telnet)

```
telnet <host> <port>
```

## docker container 접속

```
docker ps -a

CONTAINER ID   IMAGE                 COMMAND                  CREATED       STATUS       PORTS                                                           NAMES
927c11507bf8   metabase-instance3    "/app/run_metabase.sh"   10 days ago   Up 10 days   0.0.0.0:5601->3000/tcp, :::5601->3000/tcp                       metabase
62ee8a41202d   portainer/portainer   "/portainer"             10 days ago   Up 10 days   8000/tcp, 9443/tcp, 0.0.0.0:9000->9000/tcp, :::9000->9000/tcp   portainer


# container_id 및 name 확인 후 접속
docker exec -it metabase-instance3 /bin/sh


# metabase.db 폴더 백업
tar -cvf metbaseDB.tar metabase.db

# container 나가기
exit

#container -> host(docker 설치된 서버) 로 백업 파일 옮기기
docker cp metabase-instance3:/metabaseDB.tar /home/happytuk/

# 폴더 생성(같은 위치에 있어야지 잘됨.)
mkdir metabaseMigration

# tar 파일 이동
mv metabaseDB.tar ~/metabaseMigration/

# 폴더로 이동
cd metabaseMigration

# 압축 해제
tar -xvzf metabaseDB.tar 

# install 한 metabase.jar 파일도 같이 이동
mv metabase.jar ~/metabaseMigration/

# metabase migration 명령어 실행
java -DMB_DB_CONNECTION_URI="jdbc:mysql://<host>:<port>/metabase?user=<user>&password=<password>!&allowPublicKeyRetrieval=true" -jar metabase.jar load-from-h2 ./metabase.db
```

## DB metabase 스키마에 테이블 생성 되었는지 확인

## collection 테이블 조회

```
SELECT * FROM collection;
```

## 데이터 있으면 성공

## Migration 성공 후 docker container 에 있는 metabase 삭제

```
docker ps -a
CONTAINER ID   IMAGE                 COMMAND                  CREATED       STATUS       PORTS                                                           NAMES
927c11507bf8   metabase-instance3    "/app/run_metabase.sh"   10 days ago   Up 10 days   0.0.0.0:5601->3000/tcp, :::5601->3000/tcp                       metabase
62ee8a41202d   portainer/portainer   "/portainer"             11 days ago   Up 11 days   8000/tcp, 9443/tcp, 0.0.0.0:9000->9000/tcp, :::9000->9000/tcp   portainer

# 삭제 명령어 docker rm <container_id>
docker rm 927c11507bf8 
```

삭제 후 [metabase docker compose 최종본](metabase-docker-compose.md) 여기와 동일하게 yml 파일 작성 후 실행하면 된다.

***

## 에러났을 때

```
Exception in thread "main" java.lang.AssertionError: Assert failed: (#{:postgres :h2 :mysql} %) 이러한 에러가 나온다면 스키마 생성을 안
```

***

DB URI 는 mysql 로 되있지만 mariadb 도 포함 대신 URI 는 mysql 로 통일해야함.
