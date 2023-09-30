# Docker

## Docker

### Docker 란?

**Container 기반의 오픈소스 가상화 플랫폼**

#### container 란?

컨테이너는 격리된 공간에서 프로세스가 동작하는 기술

기존 방식과는 차이가 있음.

#### 기존 방식과 컨테이너 방식의 차이

**기존 VM** 같은 경우에는 Host OS 위에 가상화를 시키기 위한 Hypervisor 엔진 그리고 그 위에 Guest OS 를 올려 사용한다. 가상화된 하드웨어 위에 OS가 올라가는 형태로 Host 와 분리된다.

Host 와 완전히 분리된다는 점은 좋지만 OS 위에 OS를 올리기 때문에 무겁고 느림.

**컨테이너 기반 가상화**는 Docker 엔진 위에 Application 실행에 필요한 바이너리만 올라가게 된다.

Host의 커널만을 공유. 커널을 공유하게 되면 io 처리가 쉽게 되어 성능의 효율을 높일 수 있음.

#### Docker Image

Docker Image 란 컨테이너를 실행할수 있는 실행파일, 설정 값들을 가지고 있는 것이라고 생각하면 됨.

<figure><img src="../../.gitbook/assets/image (17).png" alt=""><figcaption></figcaption></figure>
