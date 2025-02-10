# Security

## OS Security(운영체제 보안)

운영체제 보안(OS Security) 은 악의적인 공격, 무단 접근, 데이터 유출 등을 방지하고 시스템의 무결성과 가용성을 유지하기 위한 다양한 보안 조치를 포함함.

### 핵심 개념

#### 1. 사용자 및 접근 제어(User & Access Control)

* **Root 계정 제한** : `root` 계정으로 직접 작업하지 않고 `sudo` 를 확용
* **권한 최소화(Principle of Least Privilege, PoLP)** : 필요한 최소한의 권한만 부여
* **ACL(Access Control List)활용** : 특정 사용자 또는 그룹에 대한 접근 제한을 세분화

#### 2. 파일 및 디렉토리 보안(FIle & Directory Security)

* `chmod`, `chown` , `umask` 를 사용해 파일/디렉토리 권한을 적절하게 설정
* `/etc/passwd` , `/etc/shadow` 파일 보호 : 사용자 계정 정보 유출 방지&#x20;
* 로그 파일 접근 제한(`/var/log/` 관리)

#### 3. 네트워크 보안(Network Security)

* **방화벽 설정** : `iptables`, `firewalld`, `ufw` 등을 사용해 특정 포트 차단 및 허용
* **SSH 보안 강화** :&#x20;
  * 기본 포트(22) 변경(`/etc/ssh/sshd_config` )
  * 비밀번호 로그인 대신 **SSH Key 인증 사용**
  * **Fail2Ban** 으로 SSH 공격 방지
* **VPN 및 SSH 터널링** 활용해 보안 강화

#### 4. 프로세스 및 서비스 보안(Process & Service Security)

* **불필요한 서비스 비활성화**  : `systemctl disable 서비스명`&#x20;
* **리소스 제한 설정** : `ulimit` 을 활용해 특정 프로세스의 자원 사용 제한
* **컨테이너 보안** : Docker, Kubernates 환경에서 네트워크 및 볼륨 마운트 권한 제한

#### 5. 로그 및 모니터링(Logging & Monitoring)

* `syslog` , `journald` , `auditd` 활용해 시스템 로그 관리
* **침입 탐지 시스템(IDS)** : `OSSEC` , `Snort` 등 활용
* **로그 분석 및 알림** : `Graylog` , `ELK` 활용

#### 6. 패치 및 업데이트 관리(Patch & Upadte Management)

* **보안 패치 적용** : `apt update && apt upgrade` 또는 `yum update`&#x20;
* 커널 업데이트(`kexec` 활용해 빠른 적용)
* 자동 업데이트 정책 설정(`unattended-upgrades` 활용 가능)

#### 7. 악성코드 및 취약점 스캐닝

* **파일 무결성 검사** : `tripwire` , `AIDE`&#x20;
* **백도어 탐지** : `rkhunter`, `chkrootkit`&#x20;
* **취약점 스캐닝 도구** : `Lynis` , `OpenVAS` , `Nessus`&#x20;

***

### 적용 방법

#### 1. SSH 보안 강화

```bash
sudo sed -i 's/#Port 22/Port 2222/' /etc/ssh/sshd_config
sudo systemctl restart sshd
```

#### 2. 방화벽 설정(UFW)

```bash
sudo ufw enbale

# SSH(2222 port)만 허용
sudo ufw allow 2222/tcp

# 특정 IP 만 접근 허용
sudo ufw allow from 192.168.1.10 to any port 2222
```

#### 3. 불필요한 서비스 중지

```bash
sudo systemctl list-units --type=service --start=running

# 불필요한 서비스 비활성화 (예 : FTP)
sudo systemctl disable vsftpd
```

#### 4. 로그 분석 자동화

```bash
# 특정 로그 파일에서 이상 징후 찾기(예 : SSH 실패 로그)
grep "Failed password" /var/log/auth.log | awk '{print $11}' | sort | uniq -c | sort -nr | head
```
