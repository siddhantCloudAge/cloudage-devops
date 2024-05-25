CDP 7 Installation 
------------------

### Install Basic Packages in CentOS 7 

```
sudo yum update -y
sudo yum install wget -y
sudo yum install java-devel -y 

```

### Kernal Tunning 

* VM Swappiness

```
sudo sysctl -a | grep vm.swappiness

 sudo su -c 'cat >>/etc/sysctl.conf <<EOL
 'vm.swappiness=1' 
EOL'

sudo sysctl -p

sudo sed -i '/exit 0/d' /etc/rc.local
```

* THP 

```
sudo su -c 'cat >>/etc/rc.local <<EOL
if test -f /sys/kernel/mm/transparent_hugepage/enabled; then
  echo never > /sys/kernel/mm/transparent_hugepage/enabled
fi
if test -f /sys/kernel/mm/transparent_hugepage/defrag; then
   echo never > /sys/kernel/mm/transparent_hugepage/defrag 
fi
exit 0
EOL'
sudo -i
```

### DataBase Setup 

```
wget https://dev.mysql.com/get/Downloads/Connector-J/mysql-connector-java-5.1.46.tar.gz
tar zxvf mysql-connector-java-5.1.46.tar.gz
sudo mkdir -p /usr/share/java/
cd mysql-connector-java-5.1.46
sudo cp /home/centos/mysql-connector-java-5.1.46/mysql-connector-java-5.1.46-bin.jar /usr/share/java/mysql-connector-java.jar

```


* Setup The Mysql Server (On Database Host)

```
wget https://dev.mysql.com/get/mysql57-community-release-el7-9.noarch.rpm

md5sum mysql57-community-release-el7-9.noarch.rpm

sudo rpm -ivh mysql57-community-release-el7-9.noarch.rpm

sudo yum install --nogpgcheck mysql-server -y

sudo systemctl start mysqld

sudo systemctl status mysqld

sudo grep 'temporary password' /var/log/mysqld.log

sudo mysql_secure_installation

mysqladmin -u root -p version

mysql -u root -p

exit;

```

### Create Databases for Cloudera Services 

```
CREATE DATABASE scm DEFAULT CHARACTER SET utf8 DEFAULT COLLATE utf8_general_ci;
GRANT ALL ON scm.* TO 'scm'@'%' IDENTIFIED BY 'P@ssw0rd';

SHOW DATABASES;

create database hive DEFAULT CHARACTER SET utf8;
grant all on hive.* TO 'hive'@'%' IDENTIFIED BY 'P@ssw0rd';

create database hue DEFAULT CHARACTER SET utf8;
grant all on hue.* TO 'hue'@'%' IDENTIFIED BY 'P@ssw0rd';

create database rman DEFAULT CHARACTER SET utf8;
grant all on rman.* TO 'rman'@'%' IDENTIFIED BY 'P@ssw0rd';

create database navs DEFAULT CHARACTER SET utf8;
grant all on navs.* TO 'navs'@'%' IDENTIFIED BY 'P@ssw0rd';

create database navms DEFAULT CHARACTER SET utf8;
grant all on navms.* TO 'navms'@'%' IDENTIFIED BY 'P@ssw0rd';

create database oozie DEFAULT CHARACTER SET utf8;
grant all on oozie.* TO 'oozie'@'%' IDENTIFIED BY 'P@ssw0rd';

create database actmo DEFAULT CHARACTER SET utf8;
grant all on actmo.* TO 'actmo'@'%' IDENTIFIED BY 'P@ssw0rd';

create database sentry DEFAULT CHARACTER SET utf8;
grant all on sentry.* TO 'sentry'@'%' IDENTIFIED BY 'P@ssw0rd';

CREATE USER 'temp'@'%' IDENTIFIED BY 'P@ssw0rd';

GRANT SELECT, INSERT, UPDATE, DELETE, CREATE, DROP, RELOAD, PROCESS, REFERENCES, INDEX, ALTER, SHOW DATABASES, CREATE TEMPORARY TABLES, LOCK TABLES, EXECUTE, REPLICATION SLAVE, REPLICATION CLIENT, CREATE VIEW, SHOW VIEW, CREATE ROUTINE, ALTER ROUTINE, CREATE USER, EVENT, TRIGGER ON *.* TO 'temp'@'%' WITH GRANT OPTION;

Show databases;

```


### Setup Cloudera Manager 

* Setup The Cloudera Manager Server (On CM Host)

```
sudo vi /etc/yum.repos.d/cloudera-repo.repo
```

```
[cloudera-repo]
name=cloudera-repo
baseurl= http://<httpd_server_hostname>/cm7/
enabled=1
gpgcheck=0

```

* Install Cloudera manger server and daemon 

```
sudo yum clean all

sudo yum makecache

sudo yum install cloudera-manager-server cloudera-manager-daemons -y

```

* Run DB Prepare Script 

```
sudo /opt/cloudera/cm/schema/scm_prepare_database.sh mysql -h localhost scm scm P@ssw0rd
```

* Start Cloudera Manger Server 

```
sudo service cloudera-scm-server start 
```

* In a web browser, enter the URL that the Cloudera Manager Installer displayed in the previous task: http://<server_host>:7180, where <server_host> is the FQDN or IP address of the host where the Cloudera Manager Server is running.
For example: http://ccycloud-1.streams-trial.root.hwx.site:7180
The Cloudera Manager Sign In page appears.
2. Sign in with the default credentials:
   1. Username: admin
   2. Password: admin
   3. Click Sign In.
