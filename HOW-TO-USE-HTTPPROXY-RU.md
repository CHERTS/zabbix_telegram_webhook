# �������� ����������� �� Zabbix 4.4 � Telegram ����� Webhook ��������� HTTP ������

������������� HTTP ������ � Webhook ������ ��������� ������ � Zabbix v5.0, �� � ������������� ����� (ZBXNEXT-5554) ��� ������ 4.4

���� �� ������� ������������, �� ������� ����������� Zabbix �� ���������� � ��������� ������ ����.

�c���� �� ��� ���� [�����](https://github.com/CHERTS/zabbix_telegram_webhook/tree/master/patch)

���� ���������� �� ���������� Zabbix v4.4 �� ��������� ���� ��� ������ ��.

[������ �� Oracle Linux 7 � ���������� MySQL (MariaDB)](#oracle-linux-7)

[������ �� Red Hat Enterprise Linux 8 � ���������� MySQL (MariaDB)](#red-hat-enterprise-linux-8)

[������ �� Ubuntu 18.04 LTS (Bionic Beaver) � ���������� MySQL (MariaDB)](#ubuntu)

# Oracle Linux 7
## ������ �� Oracle Linux 7 � ���������� MySQL (MariaDB)

### 1. ��� ���������� � ������ �� Oracle Linux 7 ����� ���������� �������������� ������:

~~~~
yum group install "Development Tools"
yum-config-manager --enable ol7_optional_latest
yum-config-manager --enable ol7_developer
yum install -y wget unzip gettext java-1.8.0-openjdk libxml2-devel openssl-devel libcurl-devel net-snmp-devel libevent-devel sqlite-devel pcre-devel libssh2-devel iksemel-devel OpenIPMI-devel unixODBC-devel openldap-devel
yum install -y MariaDB-client MariaDB-devel MariaDB-shared
~~~~

### 2. ������� � ����������� ������ ������ ��������� ���� � ��������� ���� ZBXNEXT-5554:

~~~~
wget https://sourceforge.net/projects/zabbix/files/ZABBIX%20Latest%20Stable/4.4.4/zabbix-4.4.4.tar.gz/download -O zabbix-4.4.4.tar.gz
tar -zxf zabbix-4.4.4.tar.gz
cd zabbix-4.4.4
wget https://raw.githubusercontent.com/CHERTS/zabbix_telegram_webhook/master/patch/ZBXNEXT-5554.patch
patch -p0 < ZBXNEXT-5554.patch
~~~~

### 3. ������ ���� ����������� Zabbix � ���������� (MariaDB) MySQL:

~~~~
./configure --with-libpthread --with-libpcre --with-libcurl --with-libxml2 --with-net-snmp --with-openssl --enable-ipv6 --with-ssh2 --with-openipmi --with-unixodbc --with-ldap --enable-server --sysconfdir=/etc/zabbix --with-mysql
make
~~~~

### 4. ����� �������� ������ �� ���� 3 ����� ������������ �������� ���� zabbix_server, ��������� ��� � ������� �������:

~~~~
cp src/zabbix_server/zabbix_server zabbix_server_mysql_v444
~~~~

�������� ������� �������� ������:

~~~~
# ls -l | grep 'zabbix_'
-rwxr-xr-x  1 root root 9768704 Jan 14 08:46 zabbix_server_mysql_v444
~~~~

������ �� ������ ���������� ���� zabbix-server � �������� ��� ������ �������.

��� �� ��� ����� �������� 1 ���� � web-frontend:
~~~~
# cp frontends/php/app/controllers/CControllerMediatypeEdit.php /your-web-frontend-root-directory/app/controllers
~~~~

# Red Hat Enterprise Linux 8
## ������ �� Red Hat Enterprise Linux 8 � ���������� MySQL (MariaDB)

### 1. ��� ���������� � ������ �� Red Hat Enterprise Linux 8 ����� ���������� �������������� ������:

~~~~
dnf group install "Development Tools"
dnf install -y wget unzip gettext java-1.8.0-openjdk java-1.8.0-openjdk-devel libxml2-devel openssl-devel libcurl-devel net-snmp-devel libevent-devel sqlite-devel pcre-devel unixODBC-devel openldap-devel
dnf install -y mariadb-devel
subscription-manager repos --enable=codeready-builder-for-rhel-8-x86_64-rpms 
yum module enable -y virt-devel 
dnf install -y libssh2-devel OpenIPMI-devel
~~~~

### 2. ������� � ����������� ������ ������ ��������� ����:

~~~~
wget https://sourceforge.net/projects/zabbix/files/ZABBIX%20Latest%20Stable/4.4.4/zabbix-4.4.4.tar.gz/download -O zabbix-4.4.4.tar.gz
tar -zxf zabbix-4.4.4.tar.gz
cd zabbix-4.4.4
wget https://raw.githubusercontent.com/CHERTS/zabbix_telegram_webhook/master/patch/ZBXNEXT-5554.patch
patch -p0 < ZBXNEXT-5554.patch
~~~~

### 3. ������ ���� ����������� Zabbix � ���������� (MariaDB) MySQL:

~~~~
./configure --with-libpthread --with-libpcre --with-libcurl --with-libxml2 --with-net-snmp --with-openssl --enable-ipv6 --with-ssh2 --with-openipmi --with-unixodbc --with-ldap --enable-server --sysconfdir=/etc/zabbix --with-mysql
make
~~~~

### 4. ����� �������� ������ �� ���� 3 ����� ������������ �������� ����� zabbix, ��������� �� � ������� �������:

~~~~
cp src/zabbix_server/zabbix_server zabbix_server_mysql_v444
~~~~

�������� ������� �������� ������:

~~~~
# ls -l | grep 'zabbix_'
-rwxr-xr-x  1 root root 9768704 Jan 14 08:46 zabbix_server_mysql_v444
~~~~

������ �� ������ ���������� ���� zabbix-server � �������� ��� ������ �������.

��� �� ��� ����� �������� 1 ���� � web-frontend:
~~~~
# cp frontends/php/app/controllers/CControllerMediatypeEdit.php /your-web-frontend-root-directory/app/controllers
~~~~

# Ubuntu
## ������ �� Ubuntu 18.04 LTS (Bionic Beaver) � ���������� MariaDB

### 1. ��� ���������� � ������ �� Ubuntu 18.04 ����� ���������� �������������� ������:

~~~~
sudo apt-get update
sudo apt-get install -y autoconf automake gcc make wget unzip gettext default-jdk libxml2-dev libssl-dev libcurl4-openssl-dev libsnmp-dev libevent-dev libsqlite3-dev libpcre2-dev libssh2-1-dev libiksemel-dev libmariadbclient-dev-compat libopenipmi-dev unixodbc-dev libldap2-dev
~~~~

### 2. ������� � ����������� ������ ������ ��������� ����:

~~~~
wget https://sourceforge.net/projects/zabbix/files/ZABBIX%20Latest%20Stable/4.4.4/zabbix-4.4.4.tar.gz/download -O zabbix-4.4.4.tar.gz
tar -zxf zabbix-4.4.4.tar.gz
cd zabbix-4.4.4
wget https://raw.githubusercontent.com/CHERTS/zabbix_telegram_webhook/master/patch/ZBXNEXT-5554.patch
patch -p0 < ZBXNEXT-5554.patch
~~~~

### 3. ������ ���� ����������� Zabbix � ���������� (MariaDB) MySQL:

~~~~
./configure --with-libpthread --with-libpcre --with-libcurl --with-libxml2 --with-net-snmp --with-openssl --enable-ipv6 --with-ssh2 --with-jabber --with-openipmi --with-unixodbc --with-ldap --enable-server --sysconfdir=/etc/zabbix --with-mysql
make
make gettext
~~~~

### 4. ����� �������� ������ �� ���� 3 ����� ������������ �������� ����� zabbix, ��������� �� � ������� �������:

~~~~
cp src/zabbix_server/zabbix_server zabbix_server_mysql_v444
~~~~

�������� ������� �������� ������:

~~~~
# ls -l | grep 'zabbix_'
-rwxr-xr-x  1 root root 9768704 Jan 14 08:46 zabbix_server_mysql_v444
~~~~

������ �� ������ ���������� ���� zabbix-server � �������� ��� ������ �������.

��� �� ��� ����� �������� 1 ���� � web-frontend:
~~~~
# cp frontends/php/app/controllers/CControllerMediatypeEdit.php /your-web-frontend-root-directory/app/controllers
~~~~
