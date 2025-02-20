# PostgreSQL scram-sha-256"

Connection matched file "/var/lib/postgresql/data/pg\_hba.conf" line 128: "host all all all scram-sha-256"

위 에러에 대한 해결방법



docker-compose.yml 에서 만들 때

```yaml
postgis:
        image: postgis/postgis:latest
        container_name: postgis
        restart: always
        environment:
            POSTGRES_DB: gis
            POSTGRES_USER: postgres
            POSTGRES_PASSWORD: your_password
            POSTGRES_HOST_AUTH_METHOD: md5
            PGDATA: /var/lib/postgresql/data/pgdata
        volumes:
         - postgis_data:/var/lib/postgresql/data
        ports:
         - "5432:5432"
```

저기서

```yaml
POSTGRES_HOST_AUTH_METHOD: md5
PGDATA: /var/lib/postgresql/data/pgdata
```

를 추가해주면 된다.
