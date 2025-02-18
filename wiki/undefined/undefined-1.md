# 일본 이관 시 문제점 해결

### Build.xml 의 부재

![image.png](https://dev.azure.com/jeffkang/c7fa7cf4-3646-4a12-922d-4a43cae15861/_apis/git/repositories/f57dcf48-f623-4e6c-802a-06f899f38955/Items?path=/.attachments/image-cba08002-9773-4cea-97b6-df056ac22166.png\&download=false\&resolveLfs=true&%24format=octetStream\&api-version=5.0-preview.1\&sanitize=true\&versionDescriptor.version=wikiMaster)

Build.xml 에 공백이 들어가 있거나 jenkins가 있는 서버에 javac 버전이 해당 소스를 빌드할때 쓰이는 자바 버전과 비슷한지 확인

## 백엔드

\
현재 바라보는 nexus 사이트의 url 이 잘못 되있었음

Grails env 에서 --stacktrace 를 넣으면 자세한 에러 확인 가능

### **Grails plugin 지원 중단**

Window 젠킨스에서 plugin 폴더 내의 내용을 전부 /var/lib/jenkins/plugin 폴더로 이동 및 xml 파일들도 다 /var/lib/jenkins 폴더로 이동

### **Ant 및 JDK 설정**

\<jenkins\_url>/configureTools/ =>(Jenkins url 경로)

### **JDK**

![image.png](https://dev.azure.com/jeffkang/c7fa7cf4-3646-4a12-922d-4a43cae15861/_apis/git/repositories/f57dcf48-f623-4e6c-802a-06f899f38955/Items?path=/.attachments/image-802781ed-0b25-46e0-8613-da06f5ed8580.png\&download=false\&resolveLfs=true&%24format=octetStream\&api-version=5.0-preview.1\&sanitize=true\&versionDescriptor.version=wikiMaster)\
경로 2개를 추가해야지 프로젝트 안에서 JDK 버전을 선택해서 할 수 있음

### **Ant 설정**

![image.png](https://dev.azure.com/jeffkang/c7fa7cf4-3646-4a12-922d-4a43cae15861/_apis/git/repositories/f57dcf48-f623-4e6c-802a-06f899f38955/Items?path=/.attachments/image-52479b51-00bd-4d2d-aa48-6a4e66c24793.png\&download=false\&resolveLfs=true&%24format=octetStream\&api-version=5.0-preview.1\&sanitize=true\&versionDescriptor.version=wikiMaster)\
Automatically 로 1.9.7 버전을 설치

### **Grails**

![image.png](https://dev.azure.com/jeffkang/c7fa7cf4-3646-4a12-922d-4a43cae15861/_apis/git/repositories/f57dcf48-f623-4e6c-802a-06f899f38955/Items?path=/.attachments/image-daba9c50-3328-46b9-9302-4946a8c9ee77.png\&download=false\&resolveLfs=true&%24format=octetStream\&api-version=5.0-preview.1\&sanitize=true\&versionDescriptor.version=wikiMaster)

### **Groovy**

![image.png](https://dev.azure.com/jeffkang/c7fa7cf4-3646-4a12-922d-4a43cae15861/_apis/git/repositories/f57dcf48-f623-4e6c-802a-06f899f38955/Items?path=/.attachments/image-7a0972fb-51ea-475b-9e66-d2eae0b7cf09.png\&download=false\&resolveLfs=true&%24format=octetStream\&api-version=5.0-preview.1\&sanitize=true\&versionDescriptor.version=wikiMaster)
