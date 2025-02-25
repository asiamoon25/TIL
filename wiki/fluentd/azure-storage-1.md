# 성능 테스트(Azure Storage)

**Azure Storage 로 로그를 백업할 때 성능 테스트**

**테스트 툴** : Jmeter(인위적 request)

**테스트 총 flush\_thread, request 수** : 1개, 10000 request

**테스트 시 사용한 url** : \<qa-site>



#### TEST 1

* **사용한 config 설정**

```shell
<source>
 @type tail
 @log_level warn
 <parse>
  @type none
 </parse>
 path <path>/localhost_access_log.%Y-%m-%d.txt
 pos_file /tmp/td-agent/<1>/access_log/log_file.pos
 tag access
</source>

<match access>
 @type azurestorage

 azure_storage_account    <account>
 azure_storage_access_key <azure storage key>
 azure_container          platformlogs
 azure_storage_type       blob
 store_as                 text
 auto_create_container    true
 path                     <path>
 azure_object_key_format  %{path}%{time_slice}_%{index}.%{file_extension}
 time_slice_format        %Y-%m-%d
 # if you want to use ${tag} or %Y/%m/%d/ like syntax in path / s3_object_key_format,
 # need to specify tag for ${tag} and time for %Y/%m/%d in <buffer> argument.
 <buffer tag,time>
   @type file
   flush_at_shutdown true
   chunk_limit_size 10k
   path /var/log/fluent/access/azurestorageappendblob
 </buffer>
</match>
```

**설정 이유**

처음에 flush 되는 txt 파일의 개수가 불규칙적 chunk\_limit\_size 를 10KB 로 주고 자주 flush 하도록 시도

**결과**

CPU 사용률 78% 까지 오름

access 뿐만 아니라, 애플리케이션 로그 등 다양한 요청이 오면 CPU 사용량이 더 오를 것으로 보임

**이유** : 잦은 flush



#### TEST 2

**사용한 config 설정**

```shell
<source>
 @type tail
 @log_level warn
 <parse>
  @type none
 </parse>
 path <path>/localhost_access_log.%Y-%m-%d.txt
 pos_file /tmp/td-agent/<1>/access_log/log_file.pos
 tag access
</source>

<match access>
 @type azurestorage

 azure_storage_account    <account>
 azure_storage_access_key <azure storage key>
 azure_container          platformlogs
 azure_storage_type       blob
 store_as                 text
 auto_create_container    true
 path                     <path>
 azure_object_key_format  %{path}%{time_slice}_%{index}.%{file_extension}
 time_slice_format        %Y-%m-%d
 # if you want to use ${tag} or %Y/%m/%d/ like syntax in path / s3_object_key_format,
 # need to specify tag for ${tag} and time for %Y/%m/%d in <buffer> argument.
 <buffer tag,time>
   @type file
   flush_mode interval
   flush_at_shutdown true
   flush_thread_count 1
   chunk_limit_size 1m
   flush_interval 10m
   path /var/log/fluent/access/azurestorageappendblob
   timekey 24h # 1시간 주기로 chunk 파일 생성 timekey 60: ["12:00:00", ..., "12:00:59"] default 10m
   timekey_wait 1m
   timekey_use_utc true # use utc
 </buffer>
</match>
```

전과 달라진 buffer 설정

```shell
<buffer tag,time>
   @type file
   flush_mode interval
   flush_at_shutdown true
   flush_thread_count 1
   chunk_limit_size 1m
   flush_interval 10m
   path /var/log/fluent/access/azurestorageappendblob
   timekey 1h # 1시간 주기로 chunk 파일 생성 timekey 60: ["12:00:00", ..., "12:00:59"] default 10m
   timekey_wait 1m
   timekey_use_utc true # use utc
 </buffer>
```

* flush\_mode interval : interval 모드를 쓰면 밑에 설정한 flush\_interval 의 시간이 흐르면 flush 를 함.
* flush\_thread\_count 1 : flush 를 하는데 사용할 쓰레드 수
* chunk\_limit\_size : chunk 파일의 제한 용량을 정함. 정한 용량을 넘어가면 자동으로 flush
* flush\_interval : 설정한 시간 10m, 10분씩 지날 때 마다 자동으로 flush
* timekey : 00:00 \~ 23:59 까지의 로그를 chunk 파일로 묶음
* timekey\_wait : time\_key 에서 설정한 시간이 지나고 flush 요청이 갈 때 몇 분 정도 기다렸다가 flush 할지의 시간

**설정한 이유**

TEST 1 에서 처럼 잦은 flush 로 인해 CPU 사용량이 78% 까지 오르는 것이 확인되어 flush 하는 chunk\_limit\_size 와 timekey 의 시간, flush\_mode 를 interval 로 설정, 잔여 로그를 방출하는 flush\_interval의 시간 등을 설정하였다.(timekey\_wait 은 없어도 되는 것으로 보임)

**TEST 1**

* timekey 와 chunk\_limit\_size 를 각각 10분(default), 10KB 를 주고 테스트

**TEST 2**

* timekey를 1시간(1h)&#x20;
* chunk\_limit\_size를 1MB(1m)&#x20;
* flush\_mode interval
* flush\_interval 을 10m(10분)
* timekey\_wait 1m(1분) 설정을 해놓고 테스트

**결과**

<figure><img src="../../.gitbook/assets/image (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

CPU 사용량 0.3 \~ 0.7 유지, flush 시 25 \~ 30% 까지 증가

flush 의 빈도수가 줄어드는 것으로 인해 영향이 별로 없을 것으로 생각





Azure LogAnalytics 는 네트워크로 로그를 한줄 한줄 보내는 것이므로 성능 테스트가 따로 필요하지 않음.
