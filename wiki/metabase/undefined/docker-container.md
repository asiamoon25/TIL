# Docker Container 버전

### 1. Docker Repository 설정 전 yum-utils 설치

```bash
sudo yum install -y yum-utils
```

### 2. yum remove

기존에 있던 Docker 삭제 후 다시 설치

```bash
sudo yum remove docker docker-client docker-client-latest docker-common docker-latest docker-latest-logrotate docker-logrotate docker-engine
```

```shell
sudo yum list | grep docker-re  # 현재 존재하지 않음
```

### 3. docker repository 추가

```shell
sudo yum-config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo
```

### 4. Repository 확인

```shell
ll /etc/yum.repos.d/
```

### 5. docker yum 확인

```shell
sudo yum list | grep docker-ce
```

### 6. docker package 설치

```shell
sudo yum install docker-ce docker-ce-cli containerd.io
```

### 7. docker systemctl 확인

```shell
systemctl status docker
```

### 8. 재기동 시 자동 실행 하도록 명령

```shell
sudo systemctl enable docker
```

### 9. Docker 실행(안켜져 있다면)

```shell
sudo systemctl start docker
```

### 10. docker compose 설치 1.27.4

```shell
sudo curl -L "https://github.com/docker/compose/releases/download/1.27.4/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
sudo chmod 755 /usr/local/bin/docker-compose
```

### 11. metabase 설치 및 컨테이너 생성, 실행

```shell
docker run -e "JAVA_OPTS= -Xms 2048m -Xmx4096m" -d -p 3000:3000 -name metabase metabase/metabase:latest
```

### 12. docker 프로세스 확인

```shell
docker ps -a
```

### 13. metabase ojdbc 플러그인 설치

1. oracle 홈페이지 가서 ojdbc8.jar 설치 후 서버에 옮기기
2. docker cp 쓰기

```
docker cp /home/happytuk/ojdbc8.jar <container_name>:/plugins/
```

3. 확인

```
docker ps -a # container id 확인 후

docker exec -it <container_id> /bin/sh # metabase 컨테이너 쉘 접속

# cd plugins # ojdbc8.jar 있는지 확인
```

4. 재실행

```
docker start <container_name>
```

