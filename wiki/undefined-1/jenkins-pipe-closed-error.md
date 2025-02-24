# JENKINS pipe closed error

com.jcraft.jsch.JSchException: java.io.IOException: Pipe closed



JENKINS 빌드 시 위 에러가 발생함.



JENKINS 가 설치된 서버로 들어가 SFTP 확인&#x20;

```shell
sftp -P <port_num> <user>@<ip0>
```

```shell
[root@jenkins-server ~]# sftp -P <port_num> <user>@<ip>
<user>@<ip0>'s password: 
Connection closed
```

연결이 바로 끊어지는 걸 확인&#x20;



### **known\_hosts** 확인&#x20;

**JENKINS SERVER**

```shell
# JENKINS SERVER
[ip1]:<port> ecdsa-sha2-nistp256 ...
[ip2]:<port> ecdsa-sha2-nistp256 ...
[ip3]:<port> ecdsa-sha2-nistp256 ...
```

연결하려던 \<ip0> 가 없는 걸 확인



**JENKINS SERVER ⇒ TARGET SERVER** 로 인증서를 복사해서 넣어줘야함.

```shell
# JENKINS SERVER
ssh-copy-id -i ~/.ssh/id_rsa.pub <user>@<ip0> -p <port>
```

다시 SFTP 명령어 실행

```shell
# JENKINS SERVER
sftp -P <port> <user>@<ip0>
<user>@<ip0>'s password: 
Connected to <ip0>.
sftp> 
```

성공&#x20;
