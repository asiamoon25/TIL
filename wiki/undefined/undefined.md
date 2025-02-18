# 한국 이관 시 문제점 해결

## JENKINS

### Publish SSH 플러그인의 사용 불가

JENKINS 버전이 올라감에 따라 Publish SSH 는 보안 문제 때문에 사용 불가능

SSH2 Easy 플러그인으로 SFTP 가능하여 SSH2 Easy 플러그인으로 교체



### Grails 플러그인 삭제

Jenkins 버전에 따라 Grails 플러그인이 더 이상 지원하지 않음.

빌드 시 JENKINS 서버에서 project 경로에 grailsw(grails sh 파일)를 놓고 명령어로 해결

```
dos2unix /var/lib/jenkins/workspace/project_name/grailsw
wait
iconv -f ISO-8859-1 -t UTF-8 /var/lib/jenkins/workspace/project_name/grailsw -o /var/lib/jenkins/workspace/project_name/grailsw
wait
chmod +x /var/lib/jenkins/workspace/project_name/grailsw
wait
/var/lib/jenkins/workspace/project_name/grailsw -Dgrails.work.dir=/var/lib/jenkins/workspace/project_name/target -Dserver.port=80 -Dgrails.env=livekr war --non-interactive --plain-output --stacktrace && exit $?
```

해당 파일은 젠킨스에서 빌드를 시작할 때마다 초기화 되므로 명령어를 심어서

1. 인코딩 변환
2. grailsw 실행권한 주기
3. grailsw 로 빌드 시작

***

##
