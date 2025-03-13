# PaaS 제품 DB 이관

Azure MariaDB 가 올해로 끝나서 이관을 해야되는데 PaaS 제품이라서 이관 지원이 안된다는 답변을 받음.

그래서 MS 직원이 추천해준 mysqlsh 방식으로 이관을 진행할 예정임.

* 필요한 재원
  * 새로운 VM (Disk I/O, RAM, CPU 등 좋은 재원으로...)
  * 특히 Disk I/O 가 큰 VM 이 필요함.

### MariaDB

일단 새로운 VM 에 mysqlsh 를 설치 후 다음과 같은 명령어를 실행한다.

```shell
mysqlsh --uri root@localhost:3307  # mariadb

\js

util.dumpInstance("\\backup", {
    threads: 16, 
    users: false, 
    consistent: true, 
    chunking: true, 
    showProgress: true, 
    excludeSchemas: ["제외할스키마"] 
})
```

* threads : MySQL 인스턴스에서 데이터 청크를 덤프하는 데 사용할 병렬 스레드 수임. 기본값은 4.
  * VM 의 CPU 상태를 보고 Threads 를 할당해야한다. 아니면 Lock 걸릴 수 있음.
* users :  (인스턴스 덤프 유틸리티 전용) 덤프에 사용자와 해당 역할 및 권한을 포함(true) 하거나 제외(false)
* consistent : 덤프 중 백업을 위해 인스턴스를 잠그어 일관된 데이터 덤프를 활성화(true) 또는 비활성화 함.
* chunking : 테이블 데이터에 대한 청킹을 활성화(true) 또는 비활성화(false) 하여 각 테이블의 데이터를 여러 파일로 분할함.&#x20;

위 명령어 실행 시 루트 디렉토리에 backup 이라는 폴더가 생성됨.

***

### MySQL

Azure Portal 의 PaaS 제품을 사용하려고 한다면 /etc/my.cnf 가 아닌 portal 에 있는 환경변수 편집 탭에서 하면됨.

<pre><code><strong>local_infile = 1
</strong>innodb_flush_log_at_trx_commit = 2
innodb_buffer_pool_size = 16G
innodb_log_file_size = 10G
innodb_doublewrite = 0
sync_binlog = 0
lock_wait_timeout = 36000
</code></pre>

해당 환경 변수를 검색하여 설정값을 변경 후 재시작



재시작이 완료 되었으면

```shell
mysqlsh --uri root@localhost:3308 # MySQL

\js

util.loadDump("\\backup", {
    loadDdl: true,
    loadData: true,
    loadUsers: false,
    ignoreVersion: true,
    analyzeTables: 'on',
    threads : 16,
    waitDumpTimeout: 36000,
    showProgress: true
})
```

이렇게 하면 됨.



#### SET GLOBAL local\_infile = ON;

`LOAD DATA LOCAL INFILE` 을 사용할 수 있도록 허용함. 원격이나 로컬 파일에서 데이터를 빠르게 삽입할 수 있음.

**성능 영향**

* 빠른 데이터 로드 가능 => `Insert` 보다 **최대 20밴 빠르게** 데이터 삽입 가능
* 대량 데이터 마이그레이션 최적화 => `.tsv.zst` 파일을 복원할 때 필수적
* 하지만 보안 위협이 있음. => 원격지에다가 파일을 로드 할 수 있으므로...

#### SET GLOBAL innodb\_flush\_log\_at\_trx\_commit = 2;

트랜잭션 커밋 시 디스크 쓰기를 줄여 성능을 높임.

* 기본값 `1` => 모든 트랜잭션 커밋마다 로그를 디스크에 기록(안전하지만 느림)
* 변경 값 `2` => 1초마다 로그를 디스크에 기록(안정성은 조금 낮추지만 빠름)

**성능 영향**

* 대량 데이터 삽입 속도 향상 => `INSERT`, `LOAD DATA INFILE` 실행 속도 최대 **2\~3배 빨라짐**
* 디스크 I/O 감소 => 트랜잭션마다 디스크 쓰기를 하지 않아서 **디스크 부담이 줄어듦**
* 하지만 데이터 유실 위험이 있음... => MariaDB 가 갑자기 크래시(정전, 장애 등) 나면 최근 1초 내의 트랜잭션이 손실 될 수도 있음.

#### SET GLOBAL innodb\_buffer\_pool\_size = 10 \* 1024 \* 1024 \* 1024 (10GB)

InnoDB의 가장 중요한 메모리 캐시 크기 조정

buffer\_pool 이 크면, 더 많은 데이터를 메모리 캐싱할 수 있어서 성능이 향상됨.

**성능 영향**

* 읽기/쓰기 성능 향상 => 더 많은 데이터를 메모리에서 처리 => 디스크 I/O 감소
* 대량 삽입(`INSERT`, `LOAD DATA`) 속도 향상 => 데이터가 캐시에 저장되면서 디스크 병목 방지
* 하지만... 메모리 부족 주의 => 대상 DB 서버의 RAM 이 16GB 이상이어야 안정적으로 사용 가능함. 예: 서버 RAM이 32GB라면 16G까지 설정 가능

```sql
SET GLOBAL innodb_buffer_pool_size = 16 * 1024 * 1024 * 1024; -- 16GB
```

#### SET sql\_log\_bin = 0;

Binary Log 기록 비활성화 기본적으로 모든 SQL 변경 사항 (`INSERT`, `UPDATE`, `DELETE`) 이 binary log 에 기록됨. => 복제 및 PITR 에 사용됨. binary\_log 를 끄고, 데이터 로드 속도를 향상 시킴.

**성능 향상**

* 대량 데이터 삽입 속도 최대 3\~5배 증가
* 불필요한 로그 생성을 방지하여 디스크 부담 감소
* 하지만... Replication (복제) 사용중이면 주의 -> binary\_log 가 꺼지면 슬레이브 서버에 복제되지 않음. Point-in-Time Recovery 불가능 => 특정 시점으로 복구하는 기능 사용할 수 없음
