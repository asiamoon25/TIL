# PostgreSQL 에러 해결

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

***

```
Osm2pgsql failed due to ERROR: CREATE UNLOGGED TABLE planet_osm_point (osm_id int8,"access" text,"addr:housename" text,"addr:housenumber" text,"addr:interpolation" text,"admin_level" text,"aerialway" text,"aeroway" text,"amenity" text,"area" text,"barrier" text,"bicycle" text,"brand" text,"bridge" text,"boundary" text,"building" text,"capital" text,"construction" text,"covered" text,"culvert" text,"cutting" text,"denomination" text,"disused" text,"ele" text,"embankment" text,"foot" text,"generator:source" text,"harbour" text,"highway" text,"historic" text,"horse" text,"intermittent" text,"junction" text,"landuse" text,"layer" text,"leisure" text,"lock" text,"man_made" text,"military" text,"motorcar" text,"name" text,"natural" text,"office" text,"oneway" text,"operator" text,"place" text,"population" text,"power" text,"power_source" text,"public_transport" text,"railway" text,"ref" text,"religion" text,"route" text,"service" text,"shop" text,"sport" text,"surface" text,"toll" text,"tourism" text,"tower:type" text,"tunnel" text,"water" text,"waterway" text,"wetland" text,"width" text,"wood" text,"z_order" int4,"tags" hstore,way geometry(POINT,3857) ) WITH ( autovacuum_enabled = FALSE ) failed: ERROR: type "hstore" does not exist
2025-02-20 22:29:07 LINE 1: ...xt,"width" text,"wood" text,"z_order" int4,"tags" hstore,way...
```

osm 을 실행하면 자꾸 저 에러가 나길래 찾아보니 hstore 타입이 없어서 그런거였음.

해당 postgresql 로 접속해서 명령어를 쳐주면 된다.

```
psql -U postgre -d gis -c "CREATE EXTENSION hstore;"

CREATE EXTENSION
```

CREATE EXTENSION 이라는 문구가 나온 뒤 osm 컨테이너를 다시 실행하면 해결된다.
