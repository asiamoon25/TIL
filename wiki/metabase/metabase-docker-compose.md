# metabase docker compose 최종본

metabase.db 백업 후 dockercompose 폴더에 넣기

**dockercomposemetabase**

```shell
$ mkdir dockercomposemetabase
$ ls
docker-compose.yml tmp
```

**tmp**

```shell
$ cd dockercomposemetabase/tmp
$ ls
log4j2.xml
```

**log4j2.xml 외 다른 파일들은 docker-compose.yml 실행 시 생성되는 파일 및 폴더들 입니다.**

tmp 폴더 생성 후 log4j2.xml 파일 생성

### docker-compose.yml

```
version: "3"
services:
  metabase-test:
    image: "metabase/metabase:v0.46.4"
    container_name: "metabase-live"
    volumes:
     - ./tmp:/tmp
    environment:
      - JAVA_OPTS=-Xmx2048m
      - MB_DB_TYPE=mysql
      - MB_DB_DBNAME=metabase
      - MB_DB_PORT=3306
      - MB_DB_USER=<db id>
      - MB_DB_PASS=<db password>
      - MB_DB_HOST=<dbhost>
      - JAVA_OPTS=-Dlog4j2.configurationFile=file:/tmp/log4j2.xml
    ports: 
      - "5601:3000"
    restart: "on-failure"
```

### log4j2.xml

```
<?xml version="1.0" encoding="UTF-8"?>
<Configuration>
  <Appenders>
    <Console name="STDOUT" target="SYSTEM_OUT" follow="true">
      <PatternLayout pattern="%date %level %logger{2} :: %message%n%throwable">
        <replace regex=":basic-auth \\[.*\\]" replacement=":basic-auth [redacted]"/>
      </PatternLayout>
    </Console>

    <!-- This file appender is provided as an example -->
    
    <RollingFile name="FILE" fileName="/tmp/metabase.log" filePattern="/tmp/metabase.log.%i">
      <Policies>
        <SizeBasedTriggeringPolicy size="500 MB"/>
      </Policies>
      <DefaultRolloverStrategy max="2"/>
      <PatternLayout pattern="%d [%t] %-5p%c - %m%n">
        <replace regex=":basic-auth \\[.*\\]" replacement=":basic-auth [redacted]"/>
      </PatternLayout>
    </RollingFile>
   
  </Appenders>

  <Loggers>
    <Logger name="metabase" level="INFO"/>
    <Logger name="metabase-enterprise" level="INFO"/>
    <Logger name="metabase.metabot" level="DEBUG"/>
    <Logger name="metabase.plugins" level="DEBUG"/>
    <Logger name="metabase.server.middleware" level="DEBUG"/>
    <Logger name="metabase.query-processor.async" level="DEBUG"/>
    <Logger name="metabase.query-processor.middleware.catch-exceptions" level="OFF"></Logger> # sql 실패 시 sql 전문과 함께 에러 로그가 뜸. OFF 설정
    <Logger name="com.mchange" level="ERROR"/>
    <Logger name="org.quartz" level="INFO"/>
    <Logger name="liquibase" level="ERROR"/>
    <Logger name="net.snowflake.client.jdbc.SnowflakeConnectString" level="ERROR"/>
    <Root level="WARN">
      <AppenderRef ref="STDOUT"/>
    </Root>
  </Loggers>
</Configuration>
```
