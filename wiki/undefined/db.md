# DB 이관

Azure 에서 사용하는 PaaS 제품으로 MariaDB 를 사용하는데 MariaDB 를 더 이상 사용하지 못한다고 한다...

그래서 MariaDB ⇒ MySQL 로의 마이그레이션을 알아보고 있는데 PaaS 제품이라는 점 때문에

이 인도인의 블로그를 보게 되었다.

{% embed url="https://techcommunity.microsoft.com/blog/adformysql/migrating-from-azure-database-for-mariadb-to-azure-database-for-mysql/3838455" %}

<figure><img src="../../.gitbook/assets/image (35).png" alt=""><figcaption></figcaption></figure>

요컨데 새로운 VM 을 생성 후 거기서 mysqlsh 명령어를 사용하여 백업한 뒤 MySQL 로 Restore 하는 방식을 알려주고 있었다.



