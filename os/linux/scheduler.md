---
description: 리눅스 스케줄러
---

# Scheduler

### 1. 스케줄러 리스트

```
crontabl -l

45 19 * * * /home/user/test.sh
17 20 * * * /home/user/test2.sh
```

현재 스케줄러가 어떤게 돌아가고 있는지 확인 가능

### 2. 스케줄러 등록할 스크립트 생성 및 작성

```
cd /home/user

sudo vi logs_bzip.sh

#실행 권한
sudo chmod +x logs_bzip.sh
```

```
#!/bin/sh

# crontab every thursday am 5:00  이런식으로 주석으로 스케줄러 스크립트의 실행 주기를 적어주는게 좋습니다.

#매일 오전 5시에 돌아가니까 전날 날짜를 기준으로 생성된 폴더에 있는 로그를 압축해야합니다.
#그래서 $date 를 사용할때 1 day ago 를 사용해서 전날 생성된 폴더의 갯수로 스크립트를 짰습니다.

LOGS_COUNT=$(find /tomcat/ -name logs_$(date -d '1 day ago' +%Y%m%d)_* | wc -l)

if [ $LOGS_COUNT != "0" ];then
    echo <server pw> | sudo -S bzip2 /tomcat/logs_$(date -d '1day ago' +%Y%m%d)_*/*.txt
elif [ $LOGS_COUNT == "0" ];then
    echo "x"
fi

exit 0
```

### 3. 스케줄러 등록

```
crontab -e
```

```
45 19 * * * /home/user/test.sh
17 20 * * * /home/user/test2.sh
# 여기에 추가해주시면 됩니다. vi 커맨드가 되서 i 를 눌러 편집해줍니다.
# 여기서 편집할 때 환경변수 등이 안먹어서 경로를 다 적어주셔야합니다.
0 5 * * * /home/user/logs_bzip.sh
```

여기서 시간을 정하는 \* \* \* \* \* 이 부분은 각각\
분 , 시 , 일 , 월 , 요일 을 나타냅니다.

```
1 * * * * => 매 시각의 1분에 실행

* / 1 * * * * => 1분 마다 실행

* 5 * * * => 매일 오전 5시 , (오후는 17로)

* */3 * * =>  3시간 마다

* * * * 2 => 화요일마다.   (0 : 일요일 ~ 6: 토요일)
```
