---
description: Linux Signal
---

# Signal

## Linux Signal

### 시그널 목록

```sh
kill -l

 1) SIGHUP       2) SIGINT       3) SIGQUIT      4) SIGILL       5) SIGTRAP
 6) SIGABRT      7) SIGBUS       8) SIGFPE       9) SIGKILL     10) SIGUSR1
11) SIGSEGV     12) SIGUSR2     13) SIGPIPE     14) SIGALRM     15) SIGTERM
16) SIGSTKFLT   17) SIGCHLD     18) SIGCONT     19) SIGSTOP     20) SIGTSTP
21) SIGTTIN     22) SIGTTOU     23) SIGURG      24) SIGXCPU     25) SIGXFSZ
26) SIGVTALRM   27) SIGPROF     28) SIGWINCH    29) SIGIO       30) SIGPWR
31) SIGSYS      34) SIGRTMIN    35) SIGRTMIN+1  36) SIGRTMIN+2  37) SIGRTMIN+3
38) SIGRTMIN+4  39) SIGRTMIN+5  40) SIGRTMIN+6  41) SIGRTMIN+7  42) SIGRTMIN+8
43) SIGRTMIN+9  44) SIGRTMIN+10 45) SIGRTMIN+11 46) SIGRTMIN+12 47) SIGRTMIN+13
48) SIGRTMIN+14 49) SIGRTMIN+15 50) SIGRTMAX-14 51) SIGRTMAX-13 52) SIGRTMAX-12
53) SIGRTMAX-11 54) SIGRTMAX-10 55) SIGRTMAX-9  56) SIGRTMAX-8  57) SIGRTMAX-7
58) SIGRTMAX-6  59) SIGRTMAX-5  60) SIGRTMAX-4  61) SIGRTMAX-3  62) SIGRTMAX-2
63) SIGRTMAX-1  64) SIGRTMAX

```

**프로세스 강제 종료 시 사용 명령어**

```
kill -9 <pid>
```

### Graceful Shutdown

위 목록에서 SIGINT, SIGTERM 에 해당

commit 을 하고 컴퓨터를 종료하는 느낌..



### Hard Shutdown

강제 종료 SIGKILL 에 해당한다.

프로세스 죽이기에는 가장 확실한 시그널.

컴퓨터 강제 종료 느낌...



### kill 옵션 리스트와 시그널 연결

| 번호 | 시그널     | 기능                     |
| -- | ------- | ---------------------- |
| 1  | SIGHUP  | 프로세스를 끊음               |
| 2  | SIGINT  | 프로세스 중지                |
| 3  | SIGQUIT | 프로세스 중단                |
| 9  | SIGKILL | 무조건 프로세스 종료            |
| 15 | SIGTERM | 가능하면 프로세스를 종료          |
| 17 | SIGSTOP | 무조건 프로세스를 중단(종료 X)     |
| 18 | SIGTSTP | 프로세스 중단 또는 일시 중지(종료 X) |
| 19 | SIGCONT | 중단된 프로세스를 실행           |



### Script 에서 사용하는 시그널

```sh
#!/bin/sh

trap <command> <signal>

ex) trap 'tail -f /var/log/log.log' 0
```

