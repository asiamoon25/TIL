# docker-compose 버전

DB 가 준비되어 있을 때

```
version: "3"
services:      
  <service-name>:
    image: "metabase/metabase:v0.46.4"
    container_name: "<container-name>"
    volumes:
      - ./metabase.db:/metabase.db  # H2 제외 한 application DB는 metabase.db 라는 파일에 저장됨. h2 는 metabase.db.mv.db
    environment:
      - JAVA_OPTS=-Xmx1024m
      - MB_DB_FILE=metabase.db
      - MB_DB_CONNECTION_URI=mysql://<dbUrl>:<port>/<dbName>?user=<user>&password=<password>&allowPublicKeyRetrieval=true
    networks:
      - "frontend"
      - "backend"
    ports: 
      - "9000:3000"
    restart: "on-failure"

networks:
  frontend:
  backend:
```

DB가 없을 때

```
version: "3"

services:
  db:
    image: mysql:8
    restart: always
    environment:
      - MYSQL_ROOT_PASSWORD=<root password>
    command:
      - --character-set-server=utf8mb4
      - --collation-server=utf8mb4_unicode_ci
    ports:
     - 8080:3306
    networks:
      - "frontend"
      
  metabase-test:
    image: "metabase/metabase:v0.46.4"
    container_name: "metabase-test1"
    volumes:
      - ./metabase.db:/metabase.db
    environment:
      - JAVA_OPTS=-Xmx1024m
      - MB_DB_FILE=metabase.db
      - MB_DB_CONNECTION_URI=mysql://db:3306/metabase?user=<user-name>&password=<password>&allowPublicKeyRetrieval=true
    depends_on:
      - db
    links:
      - db
    networks:
      - "frontend"
      - "backend"
    ports: 
      - "9000:3000"
    restart: "on-failure"

networks:
  frontend:
  backend:
```

application db 를 default 설정인 h2 로 하게 되면 설정한게 별로 없을 경우에는 빠르지만 파일 기반 이기 때문에 설정이나 dashboard 등 데이터가 많아지면 많아질 수록 느려지게 된다. 성능저하의 원인

파일을 풀 스캔 하기 때문에 CPU 사용량이 튀게 된다.\
![image.png](https://dev.azure.com/jeffkang/c7fa7cf4-3646-4a12-922d-4a43cae15861/_apis/git/repositories/f57dcf48-f623-4e6c-802a-06f899f38955/Items?path=/.attachments/image-db907047-0c55-466f-a596-139a8065b926.png\&download=false\&resolveLfs=true&%24format=octetStream\&api-version=5.0-preview.1\&sanitize=true\&versionDescriptor.version=wikiMaster)

***

docker-compose 도 container 버전과 동일하게 Oracle jar 파일을 설치 후 옮겨주면 된다.
