# Отправка уведомлений из Zabbix 4.4 в Telegram через Webhook использую HTTP прокси

Использование HTTP прокси в Webhook станет возможным только в Zabbix v5.0, но я бэкпортировал патч (ZBXNEXT-5554) для версии 4.4

Если Вы опытный пользователь, то сможете пересобрать Zabbix из исходников и применить данный патч.

Сcылка на сам патч [здесь](https://github.com/CHERTS/zabbix_telegram_webhook/tree/master/patch)

Ниже инструкции по пересборке Zabbix v4.4 из исходного кода под разные ОС.

[Сборка на Oracle Linux 7 с поддержкой MySQL (MariaDB)](#oracle-linux-7)

[Сборка на Red Hat Enterprise Linux 8 с поддержкой MySQL (MariaDB)](#red-hat-enterprise-linux-8)

[Сборка на Ubuntu 18.04 LTS (Bionic Beaver) с поддержкой MySQL (MariaDB)](#ubuntu)

# Oracle Linux 7
## Сборка на Oracle Linux 7 с поддержкой MySQL (MariaDB)

### 1. Для подготовки к сборки на Oracle Linux 7 нужно установить дополнительные пакеты:

~~~~
yum group install "Development Tools"
yum-config-manager --enable ol7_optional_latest
yum-config-manager --enable ol7_developer
yum install -y wget unzip gettext java-1.8.0-openjdk libxml2-devel openssl-devel libcurl-devel net-snmp-devel libevent-devel sqlite-devel pcre-devel libssh2-devel OpenIPMI-devel unixODBC-devel openldap-devel
yum install -y MariaDB-client MariaDB-devel MariaDB-shared
~~~~

### 2. Скачать и распаковать свежую версию исходного кода и применить патч ZBXNEXT-5554:

~~~~
wget https://sourceforge.net/projects/zabbix/files/ZABBIX%20Latest%20Stable/4.4.8/zabbix-4.4.8.tar.gz/download -O zabbix-4.4.8.tar.gz
tar -zxf zabbix-4.4.8.tar.gz
cd zabbix-4.4.8
wget https://raw.githubusercontent.com/CHERTS/zabbix_telegram_webhook/master/patch/ZBXNEXT-5554.patch
patch -p0 < ZBXNEXT-5554.patch
~~~~

### 3. Сборка всех компонентов Zabbix с поддержкой (MariaDB) MySQL:

~~~~
./configure --with-libpthread --with-libpcre --with-libcurl --with-libxml2 --with-net-snmp --with-openssl --enable-ipv6 --with-ssh2 --with-openipmi --with-unixodbc --with-ldap --enable-server --sysconfdir=/etc/zabbix --with-mysql
make
~~~~

### 4. После успешной сборки на шаге 3 можно использовать бинарный файл zabbix_server, скопируем его в текущий каталог:

~~~~
cp src/zabbix_server/zabbix_server zabbix_server_mysql_v444
~~~~

Проверим наличие бинарных файлов:

~~~~
# ls -l | grep 'zabbix_'
-rwxr-xr-x  1 root root 9768704 Jan 14 08:46 zabbix_server_mysql_v444
~~~~

Теперь Вы можете остановить свои zabbix-server и заменить его данной сборкой.

Так же Вам нужно заменить 1 файл в web-frontend:
~~~~
# cp frontends/php/app/controllers/CControllerMediatypeEdit.php /your-web-frontend-root-directory/app/controllers
~~~~

# Red Hat Enterprise Linux 8
## Сборка на Red Hat Enterprise Linux 8 с поддержкой MySQL (MariaDB)

### 1. Для подготовки к сборки на Red Hat Enterprise Linux 8 нужно установить дополнительные пакеты:

~~~~
dnf group install "Development Tools"
dnf install -y wget unzip gettext java-1.8.0-openjdk java-1.8.0-openjdk-devel libxml2-devel openssl-devel libcurl-devel net-snmp-devel libevent-devel sqlite-devel pcre-devel unixODBC-devel openldap-devel
dnf install -y mariadb-devel
subscription-manager repos --enable=codeready-builder-for-rhel-8-x86_64-rpms 
yum module enable -y virt-devel 
dnf install -y libssh2-devel OpenIPMI-devel
~~~~

### 2. Скачать и распаковать свежую версию исходного кода:

~~~~
wget https://sourceforge.net/projects/zabbix/files/ZABBIX%20Latest%20Stable/4.4.8/zabbix-4.4.8.tar.gz/download -O zabbix-4.4.8.tar.gz
tar -zxf zabbix-4.4.8.tar.gz
cd zabbix-4.4.8
wget https://raw.githubusercontent.com/CHERTS/zabbix_telegram_webhook/master/patch/ZBXNEXT-5554.patch
patch -p0 < ZBXNEXT-5554.patch
~~~~

### 3. Сборка всех компонентов Zabbix с поддержкой (MariaDB) MySQL:

~~~~
./configure --with-libpthread --with-libpcre --with-libcurl --with-libxml2 --with-net-snmp --with-openssl --enable-ipv6 --with-ssh2 --with-openipmi --with-unixodbc --with-ldap --enable-server --sysconfdir=/etc/zabbix --with-mysql
make
~~~~

### 4. После успешной сборки на шаге 3 можно использовать бинарные файлы zabbix, скопируем их в текущий каталог:

~~~~
cp src/zabbix_server/zabbix_server zabbix_server_mysql_v444
~~~~

Проверим наличие бинарных файлов:

~~~~
# ls -l | grep 'zabbix_'
-rwxr-xr-x  1 root root 9768704 Jan 14 08:46 zabbix_server_mysql_v444
~~~~

Теперь Вы можете остановить свои zabbix-server и заменить его данной сборкой.

Так же Вам нужно заменить 1 файл в web-frontend:
~~~~
# cp frontends/php/app/controllers/CControllerMediatypeEdit.php /your-web-frontend-root-directory/app/controllers
~~~~

# Ubuntu
## Сборка на Ubuntu 18.04 LTS (Bionic Beaver) с поддержкой MariaDB

### 1. Для подготовки к сборки на Ubuntu 18.04 нужно установить дополнительные пакеты:

~~~~
sudo apt-get update
sudo apt-get install -y autoconf automake gcc make wget unzip gettext default-jdk libxml2-dev libssl-dev libcurl4-openssl-dev libsnmp-dev libevent-dev libsqlite3-dev libpcre2-dev libssh2-1-dev libmariadbclient-dev-compat libopenipmi-dev unixodbc-dev libldap2-dev
~~~~

### 2. Скачать и распаковать свежую версию исходного кода:

~~~~
wget https://sourceforge.net/projects/zabbix/files/ZABBIX%20Latest%20Stable/4.4.8/zabbix-4.4.8.tar.gz/download -O zabbix-4.4.8.tar.gz
tar -zxf zabbix-4.4.8.tar.gz
cd zabbix-4.4.8
wget https://raw.githubusercontent.com/CHERTS/zabbix_telegram_webhook/master/patch/ZBXNEXT-5554.patch
patch -p0 < ZBXNEXT-5554.patch
~~~~

### 3. Сборка всех компонентов Zabbix с поддержкой (MariaDB) MySQL:

~~~~
./configure --with-libpthread --with-libpcre --with-libcurl --with-libxml2 --with-net-snmp --with-openssl --enable-ipv6 --with-ssh2 --with-jabber --with-openipmi --with-unixodbc --with-ldap --enable-server --sysconfdir=/etc/zabbix --with-mysql
make
make gettext
~~~~

### 4. После успешной сборки на шаге 3 можно использовать бинарные файлы zabbix, скопируем их в текущий каталог:

~~~~
cp src/zabbix_server/zabbix_server zabbix_server_mysql_v444
~~~~

Проверим наличие бинарных файлов:

~~~~
# ls -l | grep 'zabbix_'
-rwxr-xr-x  1 root root 9768704 Jan 14 08:46 zabbix_server_mysql_v444
~~~~

Теперь Вы можете остановить свои zabbix-server и заменить его данной сборкой.

Так же Вам нужно заменить 1 файл в web-frontend:
~~~~
# cp frontends/php/app/controllers/CControllerMediatypeEdit.php /your-web-frontend-root-directory/app/controllers
~~~~
