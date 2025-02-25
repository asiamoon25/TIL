# Tile Server 제작

{% embed url="https://osm.kr/hosting-map-tile-ubuntu2204/" %}

* 가비아에서 서버를 할당 받아서 tile-server 세팅
* Ubuntu 22.04
  * RAM : 2GB
  * CPU : 1core
  * STORAGE : 150GB(왜 이렇게 많이 필요한지는 아래에 나옴)
* 필요 데이터 : Japn-latest.osm.pbf&#x20;

위 오픈스트리트맵 지도 타일 서버 호스팅하기 에서 git clone 하는 부분까지 따라한 후 추가로 더 입력해야할 명령어가 있음.

위 호스팅하기 가이드에서는 openstreetmap-carto 에서 브랜치 v5.6.x 를 사용하고 있는것으로 보인다.

가이드에서는 openstreetmap-carto.lua 파일이 있는데 현재 master 브랜치로 받으면 에러가 난다.

#### osm2pgsql 실행

```shell
sudo -u _renderd osm2pgsql -d gis --create --slim  -G --hstore --tag-transform-script ~/src/openstreetmap-carto/openstreetmap-carto.lua -C 2500 --number-processes 1 \
    -S ~/src/openstreetmap-carto/openstreetmap-carto.style ~/data/japan-latest.osm.pbf
```

하지만 RAM 이 2GB 이므로 swap 을 사용해야함. ⇒ osm2pgsql 로 pbf 를 저장하는 동안 램을 많이 사용하기 때문

하지만 좀 더 느리게 하고 싶으면 `-C 2500` 을 `-C 512` 로 변경하면 됨.

swap 을 사용하기로 함.

```shell
# 4GB 크기의 스왑 파일 생성
sudo fallocate -l 4G /swapfile

# 스왑 파일에 권한 설정
sudo chmod 600 /swapfile

# 스왑 파일을 스왑 공간으로 설정
sudo mkswap /swapfile

# 스왑 활성화
sudo swapon /swapfile

# 스왑이 추가되었는지 확인
swapon --summary

# 영구적으로 유지하려면 /etc/fstab 파일에 추가
echo '/swapfile none swap sw 0 0' | sudo tee -a /etc/fstab
```



