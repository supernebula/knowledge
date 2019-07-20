# Aliyun

## Aliyun RDS

### RDS FOR MYSQL 各版本利用物理备份进行本地恢复的方法


下面文档讲述RDS FOR MYSQL 各版本利用物理备份进行本地恢复的方法：

说明：

1. 本地自建Mysql数据必须和云mysql版本大版本保持一致

2. 64位的Linux系统

3. MySQL 5.6及之前（含5.5）的版本需要安装 Percona XtraBackup 2.3 ； MySQL 5.7版本需要安装 Percona XtraBackup 2.4

4. 为了快速恢复，本地mysql安装都采用centos的yum安装方式





5.7/5.6恢复示例：

安装mysql:

1.下载yum源

centos6系统执行下面下载安装：

```shell
wget  https://repo.mysql.com//mysql80-community-release-el6-1.noarch.rpm
yum localinstall mysql80-community-release-el6-1.noarch.rpm

centos7系统执行下面下载安装:
wget  https://repo.mysql.com//mysql80-community-release-el7-1.noarch.rpm

yum localinstall mysql80-community-release-el7-1.noarch.rpm
```


2.配置yum源

```shell
yum -y install yum-utils
```


#根据选择安装需要的版本

```shell
yum-config-manager --disable   mysql80-community  默认是安装8.0版本的mysql需要关闭这个yum源

yum-config-manager --enable   mysql57-community  恢复5.7版本就执行这条

yum-config-manager --enable   mysql56-community   如果是恢复5.6版本执行这条
```


3.安装mysql server (yum安装完成后不慌启动mysql)

```shell
yum install mysql-community-server
```

4.替换/etc/my.cnf文件，其中包括跳过权限表启动的配置项：

my.cnf

```ini

[mysqld]

innodb_checksum_algorithm=crc32

innodb_data_file_path=ibdata1:200M:autoextend

innodb_log_files_in_group=2

innodb_log_file_size=524288000

innodb_undo_directory=/var/lib/mysql/

basedir=/usr

datadir=/var/lib/mysql

innodb_undo_tablespaces=0

server_id=999098802
skip-grant-tables=1

sql_mode=''

```



1).配置yum源

```shell
yum install http://www.percona.com/downloads/percona-release/redhat/0.1-3/percona-release-0.1-3.noarch.rpm
```

2). 查看yum源，选择要安装的软件

```shell
yum list | grep percona

yum -y install percona-xtrabackup-24.x86_64   5.7版本需要安装这个
yum -y install percona-xtrabackup.x86_64        5.6版本需要安装这个
```


6.下载备份文件并恢复：

wget -c 'rds备份文件下载地址' -O bak.tar.gz     这个到RDS控制台备份恢复里面去获取备份集下载地址，注意下载地址这里用wget下载需要用单引号引起来，否则会报403错误
      

tar -izxvf bak.tar.gz    -C   /var/lib/mysql       在下载的备份文件目录执行解压，-C指定解压到/var/lib/mysql目录，因为yum安装的datadir是这个

```shell
cd  /var/lib/mysql

innobackupex --defaults-file=backup-my.cnf --apply-log /var/lib/mysql

chown -R mysql:mysql /var/lib/mysql

/etc/init.d/mysqld start

```

更新aliyun_root@127.0.0.1密码：

mysql     #shell里面执行mysql连接mysql并修改密码
mysql>select user,host from mysql.user;   查看是否有aliyun_root@127.0.0.1这个用户，可根据需要来设置根据这个sql查看到有的账号密码

mysql>flush privileges;

mysql>SET PASSWORD FOR 'aliyun_root'@'127.0.0.1' = PASSWORD('MyNewPass');

mysql>exit


删除/etc/my.cnf里面的skip-grant-tables=1​​​​​​

/etc/init.d/mysqld restart

账号也迁移过来了的，可以用账号云mysql的账号登陆了：

​

登陆：

mysql -ualiyun_root -h127.0.0.1 -pMyNewPass       这里注意登陆需要用到-h127.0.0.1，因为RDS里面没有localhost主机的链接地址，不指定会报错

mysql>use mysql;

mysql>alter table proc engine=myisam;

mysql>alter table event engine=myisam;

mysql>alter table  func engine=myisam;


5.5版本的恢复：

```shell
1)  wget  https://repo.mysql.com//mysql80-community-release-el6-1.noarch.rpm

2)   yum localinstall mysql80-community-release-el6-1.noarch.rpm

3)   yum -y install yum-utils

4)   yum-config-manager --disable mysql80-community

5)   yum-config-manager --enable mysql55-community

6)   yum install mysql-community-server -y

7)   vi /etc/my.cnf   添加下面内容，注意区别于5.6，5.7版本的内容

```

```ini
[mysqld]

innodb_data_file_path=ibdata1:200M:autoextend

innodb_log_files_in_group=2

innodb_log_file_size=524288000

basedir=/usr

datadir=/var/lib/mysql

server_id=999098802

skip-grant-tables=1

sql_mode=''
```


步骤3：

安装percona2.3

```shell
1）yum install http://www.percona.com/downloads/percona-release/redhat/0.1-3/percona-release-0.1-3.noarch.rpm

2）yum install percona-xtrabackup.x86_64 
```



步骤4：

准备好mysql5.5的物理备份安装包，省略

假设这里获取的物理备份文件名字叫：a.tar.gz



步骤5：

开始恢复


```shell
1）tar -izxvf a.tar.gz  -C /var/lib/mysql

2)  cd /var/lib/mysql

3)  innobackupex --defaults-file=backup-my.cnf --apply-log /var/lib/mysql

4)  chown -R mysql:mysql /var/lib/mysql

5)  /etc/init.d/mysqld start

6)  mysql    直接进入mysql命令会话，进入后依次执行下面命令

mysql> flush privileges;

Query OK, 0 rows affected (0.00 sec)


mysql> select user,host from mysql.user;

+---------------+--------------+

| user          | host         |

+---------------+--------------+

| aurora        | %            |

| replicator    | 10.151.82.81 |

| root          | 127.0.0.1    |

| root          | ::1          |

| rdsflashback  | localhost    |

+---------------+--------------+

8 rows in set (0.00 sec)


mysql> SET PASSWORD FOR 'root'@'127.0.0.1' = PASSWORD('MyNewPass');

Query OK, 0 rows affected (0.00 sec)


mysql> exit

```


可以再登陆mysql查看数据库信息：

```shell

mysql -uroot -pMyNewPass -h127.0.0.1

mysql>use mysql;

mysql>alter table proc engine=myisam;

mysql>alter table event engine=myisam;

mysql>alter table  func engine=myisam;

mysql>show databases;

```
