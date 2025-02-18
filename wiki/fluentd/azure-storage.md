# Azure Storage 로 로그 백업

**/var/log/<1>/<2>/<3>/yyyy-mm-dd.txt** 형식으로 로그가 쌓임

각 컨트롤러, 메서드 별로 폴더가 생성되기 때문에 동적으로 할당되도록 설정해야함.

1, 2 는 고정이기 때문에 그냥 두고 동적인 데이터만 \*로 함.

```shell
<source>
 @type tail
 @log_level warn
 <parse>
  @type none
 </parse>
 path /var/log/<1>/<2>/*/*/%Y-%m-%d.txt
 pos_file /tmp/td-agent/<1>/<2>/log_file.pos
 tag api.*
</source>

<match api.**>
 @type azurestorage

 azure_storage_account    stvmhappytuk
 azure_storage_access_key <key>
 azure_container          platformlogs
 azure_storage_type       blob
 store_as                 text
 auto_create_container    true
 path                     qa/<1>/<2>/${tag[4]}/${tag[5]}/${tag[6]}/
 azure_object_key_format  %{path}%{time_slice}_%{index}.%{file_extension}
 time_slice_format        %Y-%m-%d
 # if you want to use ${tag} or %Y/%m/%d/ like syntax in path / s3_object_key_format,
 # need to specify tag for ${tag} and time for %Y/%m/%d in <buffer> argument.
 <buffer tag,time>
   @type file
   flush_interval 50
   flush_at_shutdown true
   path /var/log/fluent/api/azurestorageappendblob
   timekey 5 # 1 hour partition
   timekey_use_utc true # use utc
 </buffer>
</match>
```

\<source>의 tag 부분에 저렇게 .\*이 들어가게 되면 path /var... 이 태그의 \* 부분에 들어간다.

api.path.var. ...

그 다음 \<match> 부분에 tag의 인덱스 번호를 줘서

```
path                     qa/happycode/ht-qa/${tag[4]}/${tag[5]}/${tag[6]}/
```

path 설정을 해주면 이제 해당 폴더에 로그가 쌓이면 해당 폴더명으로 azure storage에도 쌓이게 된다.
