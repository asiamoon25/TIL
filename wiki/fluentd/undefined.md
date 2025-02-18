# 설치 순서

**서버 스펙**

CentOS 6.10 버전

### 1. 서버 nofile nproc 확인

```sh
sudo cat /etc/security/limits.conf

# 현재 유저 및 root 유저의 nofile, nproc 확인
*               soft     nofile         65536
*               hard     nofile         65536
user        soft     nofile         65536
user        hard     nofile         65536
```

nofile 개수는 65536 으로 맞춘다. ⇒ Fluentd 공식 홈페이지에 설치 방법에 쓰여있음.



### 2. 사전 준비

fluentd 는 rpm 설치도 있지만 ruby 를 설치해서 gem 을 이용하여 설치하는 경우도 있음.

rpm 만으로 설치하는 경우 높은 버전이 아니라면 특정 plugin이 컴파일이 안되어 Exception 만 나오는 경우가 있으므로 ruby 를 설치해서 gem 으로 fluentd 를 설치해야한다.

현재 회사의 서버 버전은 CentOS 6.10 이므로 호환되지 않는 라이브러리가 있으니 버전업을 해줘야한다.

```shell
# 필수 라이브러리 설치
sudo yum install git-core zlib zlib-devel gcc-c++ patch readline readline-devel libyaml-devel libffi-devel openssl-devel make bzip2 autoconf automake libtool bison curl sqlite-devel
```

위 명령어 대로 설치를 안하면 에러가 난다. glib 버전이 낮아 설치가 안된다고 한다.

#### Ruby 설치

**rbenv 설치**

rbenv 라는 걸로 ruby 를 설치한다. rvm 을 사용하지 않는 이유는 rbenv가 더 가볍다는 이유가 있음.

```shell
sudo curl -fsSL https://github.com/rbenv/rbenv-installer/raw/HEAD/bin/rbenv-installer | bash
```

**ruby 환경 변수 설정**

```shell
echo 'export PATH="$HOME/.rbenv/bin:$PATH"' >> ~/.bashrc
echo 'eval "$(rbenv init -)"' >> ~/.bashrc
source ~/.bashrc

저 위에 걸 하기 싫다면 

sudo vi /etc/profile(전 사용자) 또는 현재 사용자에만 설정하고 싶다면 sudo vi ~/.bashrc 후

맨 밑에 
export PATH="$HOME/.rbenv/bin:$PATH"
eval "$(rbenv init -)" 한 후 :wq! 로 저장 후에 

source /etc/profile 또는 source ~/.bashrc
```

**설치 가능 버전 확인**

```shell
rbenv install -l

2.6.10
2.7.3
3.0.1
jruby-9.2.19.0
mruby-3.0.0
rbx-5.0
truffleruby-21.1.0
truffleruby+graalvm-21.1.0
```

**설치 후 global 등록**

```shell
rbenv install 2.6.10

rbenv global 2.6.10

ruby --version
2.6.10
```

설치가 완료되었다면 \~/.rbenv/versions/2.6.10/bin 으로 이동 후

```shell
sudo ./gem install nokogiri --platform=ruby 
```

명령어로 컴파일이 완료된 것을 설치해줘야 한다. 그러면 컴파일 과정을 거치지 않고 넘어간다.

컴파일되지 않은 걸로 설치하면 CentOS 버전이 낮기 때문에 glib 버전이 낮아 에러가 나게 된다.

**플러그인 설치**

* 플러그인 설치 방법

```shell
sudo ./gem install <plugin-name> [option]
```

* 설치 명령어

<pre class="language-shell"><code class="lang-shell"><strong>sudo ./gem install fluentd -v 1.15.0    # -v 옵션 없이 install 시 최신 버전으로 설치됨
</strong>
sudo ./gem install fluent-plugin-azurestorage
sudo ./gem install fluent-plugin-azure-loganalytics
</code></pre>

Fluentd 실행 명령어 및 옵션

```shell
# command : sudo ./fluentd -h 누르면 나옴
Usage: fluentd [options]
    -s, --setup [DIR=/etc/fluent]    디렉토리에 샘플 구성 파일 설치
    -c, --config PATH                설정파일 경로 (default: /etc/fluent/fluent.conf)
        --dry-run                    설정파일 검사(fluentd 실행 x)
        --show-plugin-config=PLUGIN  [DEPRECATED] Show PLUGIN configuration and exit(ex: input:dummy)
    -p, --plugin DIR                 add plugin directory
    -I PATH                          add library path
    -r NAME                          load library
    -d, --daemon PIDFILE             fluentd 데몬화

        --under-supervisor           run fluent worker under supervisor (this option is NOT for users)
        --no-supervisor              run fluent worker without supervisor
        --workers NUM                specify the number of workers under supervisor
        --user USER                  user 변경
        --group GROUP                group 변경
        --umask UMASK                change umask
    -o, --log PATH                   fluentd 로그 파일 경로
        --log-rotate-age AGE         generations to keep rotated log files
        --log-rotate-size BYTES      sets the byte size to rotate log files
        --log-event-verbose          enable log events during process startup/shutdown
    -i CONFIG_STRING,                inline config which is appended to the config file on-the-fly
        --inline-config
        --emit-error-log-interval SECONDS
                                     suppress interval seconds of emit error logs
        --suppress-repeated-stacktrace [VALUE]
                                     suppress repeated stacktrace
        --without-source             invoke a fluentd without input plugins
        --use-v1-config              Use v1 configuration format (default)
        --use-v0-config              Use v0 configuration format
        --strict-config-value        Parse config values strictly
        --enable-input-metrics       Enable input plugin metrics on fluentd
        --enable-size-metrics        Enable plugin record size metrics on fluentd
    -v, --verbose                    increase verbose level (-v: debug, -vv: trace)
    -q, --quiet                      decrease verbose level (-q: warn, -qq: error)
        --suppress-config-dump       suppress config dumping when fluentd starts
    -g, --gemfile GEMFILE            Gemfile path
    -G, --gem-path GEM_INSTALL_PATH  Gemfile install path (default: $(dirname $gemfile)/vendor/bundle)
        --conf-encoding ENCODING     specify configuration file encoding
        --disable-shared-socket      Don't open shared socket for multiple workers

```

**Fluentd 실행 명령어**

```shell
sudo ./fluentd --user root --group root --daemon /var/run/fluentd.pid
```

user, group 을 root 로 한 이유는 권한 문제 때문에 root 로 설정해서 했음.

**azure-storage, azure-loganalytics 설치**

```shell
sudo ./gem install fluent-plugin-azurestorage

sudo ./gem install fluent-plugin-azure-loganalytics
```



**서비스 등록**

fluentd 를 실행할 때 마다 위 명령어를 쓰기 귀찮으니 서비스 등록을 해줘야 한다.

* 파일 생성

```shell
sudo vi fluentd
```

* script 작성

```shell
#!/bin/sh   

# chkconfig: 2345 90 90 
# description: init file for fluentd
# processname: fluentd

RETVAL=0 
PNAME=fluentd 
PID=`ps -ef| grep "^.*\/home/user/.rbenv/versions/2.6.10/bin/fluentd --user root --group root --daemon /var/run/fluentd.pid$" | grep -v 'grep' | awk '{print $2}'` 
case "$1" in 
        start)
                echo /home/user/.rbenv/versions/2.6.10/bin/fluentd --user root --group root --daemon /var/run/fluentd.pid
                RETVAL=$?
                echo $RETVAL
      ;;

        stop)
                if [ $PID -gt 0 ];then
                        echo /home/user/.rbenv/versions/2.6.10/bin/fluent-ctl shutdown $PID
                        RETVAL=$?
                elif [ -z $PID ];then
                        echo fluentd is not running
                fi
      ;;
        restart)
                if [ $PID -gt 0 ];then
                        echo /home/user/.rbenv/versions/2.6.10/bin/fluent-ctl restart $PID
                        RETVAL=$?
                elif [ -z $PID ];then
                        echo fluentd is not running
                fi
      ;;
        *)
                echo "Usage: fluentd {start|stop|restart}"
                exit 1
      ;;
    esac
        echo $RETVAL
    exit $RETVAL

```

* 작성한 스크립트 파일 /etc/init.d 로 이동

```shell
sudo mv fluentd /etc/init.d/
```

* 실행 권한 부여

```shell
sudo chmod -R 755 /etc/init.d/fluentd
sudo chmod +x /etc/init.d/fluentd # 실행 권한
```

* 서비스 등록

```shell
cd /etc/init.d
sudo chkconfig --add fluentd
```

* 서비스 전체 리스트 확인

```shell
sudo chkconfig --list
```

```shell
# 해당 서비스 만 확인
sudo chkconfig --list fluentd
```

* 실행&#x20;

```shell
sudo service fluentd start
```
