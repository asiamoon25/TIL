# Teams Alert

기존 서버가 장애로 인해 죽었을 때

<figure><img src="../../.gitbook/assets/image (30).png" alt=""><figcaption></figcaption></figure>

이런식으로 팀즈 메신저 알람이 옴.



이렇게 메시지가 올 때 \
\
발견자⇒ 기술이사님 또는 기술PM 호출 ⇒ 개발팀 호출 ⇒ 문제 해결

이런 방식으로 문제를 해결해 왔는데 호출을 조금이라도 줄이기 위해 shell script 로 스케줄러를 돌려 서버 상황을 5분 또는 30분 에 한번 씩 확인 후 팀즈 알림으로 보내는 스케줄러를 제작

<figure><img src="../../.gitbook/assets/image (31).png" alt=""><figcaption></figcaption></figure>

**결과**

tomcat 프로세스를 체크해서 tomcat 프로세스가 죽었을 때 알림을 보내는 스크립트 작성 후 서버 떨어지면 바로 응대가 가능

***

기존 서버 용량 부족으로 인해 로그가 쌓이지 않고 장애 발생으로 서버가 죽는 상황이 발생

이러한 상황을 방지하고자 server size 를 체크해서 알림으로 보내는 스케줄러 작성

<figure><img src="../../.gitbook/assets/image (32).png" alt=""><figcaption></figcaption></figure>

<pre class="language-shell"><code class="lang-shell">################################################################################
<strong>
</strong>################################################################################

PASSWD="password"
##################################################
# /home/happytuk
##################################################
curDir=$(pwd)
SSH_STDIN=$curDir/ssh_stdin
#
# for SSH
sshOption="-T -o StrictHostKeyChecking=no -o ConnectTimeout=1 -o NumberOfPasswordPrompts=1"
#
##################################################
# SAMPLE: SVR_LIST="172.25.49.135 172.25.49.136"
##################################################
SVR_LIST="ip1 ip2 ip3 ip4 ip5"

mpList=$(eval echo $SVR_LIST)
##################################################
# 전달받은 전송할 URL 주소를 입력한다=> $1
##################################################
RESULT_LIST=""

for idx in $mpList
do
    #echo "IP:::"$idx
    #vvv=$(bash -c -s "cat &#x3C;/dev/null> /dev/tcp/${idx}/22")
    _STATUS=$(timeout 1 bash -c "cat &#x3C; /dev/null > /dev/tcp/${idx}/22"; if [ $? -gt 0 ]; then echo "fail"; else echo "ok"; fi);

    if [ "$_STATUS" == "ok" ]; then
        checkingVal=$(echo $PASSWD | $SSH_STDIN ssh $sshOption "user@"$idx "df -lh /* |grep '/dev/sda1' |  head -1 | awk '{print \$5}' | sed 's/%//g'")
        #echo "IP:::$idx:$checkingVal"
        if [ $checkingVal -ge 90 ];then
           RESULT_LIST+="${idx} : ${checkingVal}% \n\n"
        fi
    else
           RESULT_LIST+="${idx} : SSH 접속시 오류가 발생하였습니다.\n\n"
    fi
done

RESULTLENGTH=`echo $RESULT_LIST | awk '{print length($0)}'`
if [ $RESULTLENGTH -gt 0 ];then
curl -d '{"@context":"https://schema.org/extensions","@type":"MessageCard","themeColor":"#0072C6","title": "[NOTICE] azure vmss Sever DISK 체크", "text": "'"$RESULT_LIST"'" }' -H "Content-Type: Application/JSON" -X POST &#x3C;webhook url>

fi

exit 0
</code></pre>
