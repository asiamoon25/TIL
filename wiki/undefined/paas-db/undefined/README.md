# 프로세스

### 재원

#### Ubuntu

* Ubuntu
* CPU 16v
* RAM 64GB
* SSD 5TB

#### MariaDB

* Azure PaaS 제품
* 홍콩 리전

#### MySQL

* Azure PaaS 제품
* 대만 리전
* Ubuntu 와 리전이 같고 네트워크가 같은 곳에 있음.

***

### 리전이 다름

MariaDB 와 Ubuntu 간 네트워크 지연시간이 있으므로 export 에는 시간이 많이 걸리고 MySQL 과는 시간이 짧을 것으로 보임.

***

### 순서

#### 1. Ubuntu 에 mysqlsh 설치

1-1) MySQL APT 저장소 추가

```shell
wget https://dev.mysql.com/get/mysql-apt-config_0.8.29-1_all.deb
sudo dpkg -i mysql-apt-config_0.8.29-1_all.deb
sudo apt update
```

1-2) MySQL Shell 설치

```shell
sudo apt install mysql-shell -y
```

1-3) 설치 확인

```shell
mysqlsh --version
```

1-4) 자바스크립트 모드 실행 확인

```shell
mysqlsh --js
```

1-5) 접속

```shell
mysqlsh --uri <user>@<host>:<port>
```

#### 2. export 시작

util.dumpInstance 실행

<pre class="language-shell"><code class="lang-shell"><strong>js> util.dumpInstance("/datadrive/backup", {
</strong>    threads: 16, 
    users: false,
    chunking: true, 
    showProgress: true
})
</code></pre>

#### 3. import 시작

```shell
mysqlsh --uri <mysqlUser>@<mysqlHost>:<mysqlPort>

sql> \js

js> util.loadDump("/datadrive/backup", {
    loadDdl: true,
    loadData: true,
    loadUsers: false,
    ignoreVersion: true,
    analyzeTables: 'on',
    threads: 16,
    waitDumpTimeout: 36000,
    showProgress: true
})
```

#### 4. dump 떠진 폴더로 접근 후 @.json 파일 확인

```shell
cd /datadrive/backup

cat \@.json

{
    "dumper": "mysqlsh Ver 8.0.41 for Linux on x86_64 - for MySQL 8.0.41 (MySQL Community Server (GPL))",
    "version": "2.0.1",
    "origin": "dumpInstance",
    "options": {
        "chunking": true,
        "showProgress": true,
        "threads": 16,
        "users": false
    },
    "schemas": [
        ...
    ],
    "basenames": {
        ...
    },
    "defaultCharacterSet": "utf8mb4",
    "tzUtc": true,
    "bytesPerChunk": 64000000,
    "user": "<username>",
    "hostname": "<hostname>",
    "server": "CLIENT",
    "serverVersion": "5.6.0-10.2.32-MariaDB-log",
    "binlogFile": "mysql-bin.003549",
    "binlogPosition": 8562,
    "gtidExecuted": "",
    "gtidExecutedInconsistent": false,
    "consistent": true,
    "partialRevokes": false,
    "compatibilityOptions": [],
    "capabilities": [],
    "begin": "2025-03-12 05:01:55"
```

**binlogFile**, **binlogPosition** 을 체크 해야함.

#### 5.원본 DB 와의 복제 걸기

```sql
CALL mysql.az_replication_change_master('<host>', '<username>', '<password>', 3306, 'mysql-bin.003549', 8562, '');

# 복제 시작 시
CALL mysql.az_replication_start;

# 복제 확인 하려면
show slave status\G

# 복제 중지 
CALL mysql.az_replication_stop;

# 마스터 삭제
CALL mysql.az_replication_remove_master;
```

**확인 사항**

```shell
 MySQL <host>:3306 ssl  SQL > show slave status\G
*************************** 1. row ***************************
               Slave_IO_State: Waiting for master to send event
                  Master_Host: <host>
                  Master_User: <user>
                  Master_Port: 3306
                Connect_Retry: 60
              Master_Log_File: mysql-bin.003549
          Read_Master_Log_Pos: 9439
               Relay_Log_File: relay_bin.000002
                Relay_Log_Pos: 1330
        Relay_Master_Log_File: mysql-bin.003549
             Slave_IO_Running: Yes
            Slave_SQL_Running: Yes
        ... 
        Seconds_Behind_Master: 0
        ...
1 row in set (0.0006 sec)
```

**Slave\_IO\_Running** : Yes

**Slave\_SQL\_Running** : Yes

**Seconds\_Behind\_Master** : 0\
\
위 3개의 값을 확인해야함. Master 와 연결이 안되면 IO 와 Second 값이 NULL 이고

**Last\_IO\_Errno: 1045**\
**Last\_IO\_Error: error connecting to master 'b66f3@e8829:3306' - retry-time: 60  retries: 1**\
\
**이러한 값들이 추가되면서 복제가 안된다.**
