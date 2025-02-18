# Azure LogAnalytics

fluentd 에서 access\_log 에 있는 로그들을 key-value 형태로 만들기 위해 expression 을 사용

```shell
<source>
  @type tail
  <parse>
  @type regexp
  expression /^\[(?<timestamp>\d{4}\-\d{2}\-\d{2} \d{2}\:\d{2}\:\d{2})\] \[(?<host_client_ip>.*?)\] \[(?<param>.*?)\] \[(?<header>.*?)\]$/
 </parse>
  path /var/log/<1>/<2>/*/*/%Y-%m-%d.txt
  pos_file /tmp/fluent/happycode/analytics/api-req/log_file.pos
  tag request.*
</source>

<match response.**>
    @type azure-loganalytics
    customer_id <customer_id>
    shared_key <shared_key>
    log_type QAAccessLog
    add_time_field true
    time_field_name testtime
    time_format %FT%T%z
    localtime true
    add_tag_field true
    tag_field_name api
</match>



<match request.**>
    @type azure-loganalytics
    customer_id <customer_id>
    shared_key <shared_key>
    log_type QAAccessLog
    add_time_field true
    time_field_name testtime
    time_format %FT%T%z
    localtime true
    add_tag_field true
    tag_field_name api
</match>
```

