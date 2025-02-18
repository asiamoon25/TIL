# swap 0k(free 0k)

![image.png](https://dev.azure.com/jeffkang/c7fa7cf4-3646-4a12-922d-4a43cae15861/_apis/git/repositories/f57dcf48-f623-4e6c-802a-06f899f38955/Items?path=/.attachments/image-eac6f1ae-7c38-427f-9073-f3e5624494bc.png\&download=false\&resolveLfs=true&%24format=octetStream\&api-version=5.0-preview.1\&sanitize=true\&versionDescriptor.version=wikiMaster)

이 문제로 UNSTABLE 나올 시

sudo kill -9 PID 로 해당 톰캣 죽이고

쓸모없는 애들 죽인다.

ps -ef |grep tomcat

```
root     27593 27526  0 12:31 ?        
root     28913 28459  0 12:37 pts/5  
root     32174     1  3 09:00 pts/1   
```

front tomcat 죽이고 , tail 죽이고 그 후에 다시 젠킨스 build

tail -f /usr/local/tomcat7/logs/catalina.out 시

/home/happytuk/backupWar/hs\_err\_pid27591.log 이런 문구가 마지막에 떨어지는데 이것을 보면

```
Memory: 4k page, physical 8061220k(188548k free), swap 0k(0k free)

vm_info: Java HotSpot(TM) 64-Bit Server VM (24.80-b11) for linux-amd64 JRE (1.7.0_80-b15), built on Apr 10 2015 19:53:14 by "java_re" with gcc 4.3.0 20080428 (Red Hat 4.3.0-8)

time: Fri Feb 10 12:31:02 2023
elapsed time: 0 seconds
```

이렇게 나오는데 swap 0k(0k free) 되 있으면 위 방법을 실행
