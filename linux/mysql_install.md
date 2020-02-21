# Centos7 安装mysql

参考：

https://www.cnblogs.com/xuanzhi201111/p/5148113.html

https://www.cnblogs.com/xiongnanbin/p/11834979.html

## 压缩版安装

[下载 MySQL Community Server 5.7](https://downloads.mysql.com/archives/community/)

1. 下载
```shell
# cd /usr/local
# wget https://downloads.mysql.com/archives/get/p/23/file/mysql-5.7.28-el7-x86_64.tar.gz
```
2. 解压，创建目录

解压, 修改目录名称为mysql
```shell 

# tar zxvf mysql-5.7.28-el7-x86_64.tar.gz
# mv zxvf mysql-5.7.28-el7-x86_64 mysql
```

创建data目录

```shell
# cd mysql
# mkdir data
```

自定义mysql数据目录，而是放在第二块磁盘 /opt/mysql下

```shell
# cd /opt/mysql
# mkdir data
```

4. 创建mysql用户和用户组，设置用户权限
```shell
# groupadd mysql
# useradd -r -s /sbin/nologin -g mysql mysql -d /usr/local/mysql/
```
用户组mysql，用户mysql

useradd -r参数表示mysql用户是系统用户，不可用于登录系统

改变mysql目录权限,之前是root权限，现在设置成mysql权限
```shell
# chown -R mysql:mysql /usr/local/mysql/
# cd mysql
# ll
total 308
drwxr-xr-x  2 mysql mysql   4096 Feb 21 13:15 bin
drwxr-xr-x  2 mysql mysql   4096 Feb 21 13:15 docs
drwxr-xr-x  3 mysql mysql   4096 Feb 21 13:15 include
drwxr-xr-x  5 mysql mysql   4096 Feb 21 13:15 lib
-rw-r--r--  1 mysql mysql 279547 Sep 27 15:00 LICENSE
drwxr-xr-x  4 mysql mysql   4096 Feb 21 13:15 man
-rw-r--r--  1 mysql mysql    587 Sep 27 15:00 README
drwxr-xr-x 28 mysql mysql   4096 Feb 21 13:15 share
drwxr-xr-x  2 mysql mysql   4096 Feb 21 13:15 support-files

```


5. 初始化数据库
```shell
# cd /usr/local/mysql
# ./bin/mysqld --initialize --user=mysql --basedir=/usr/local/mysql/ --datadir=/opt/mysql/data/
```
执行输出
```shell
2020-02-21T07:14:08.790048Z 0 [Warning] TIMESTAMP with implicit DEFAULT value is deprecated. Please use --explicit_defaults_for_timestamp server option (see documentation for more details).
2020-02-21T07:14:09.768186Z 0 [Warning] InnoDB: New log files created, LSN=45790
2020-02-21T07:14:09.889558Z 0 [Warning] InnoDB: Creating foreign key constraint system tables.
2020-02-21T07:14:09.954803Z 0 [Warning] No existing UUID has been found, so we assume that this is the first time that this server has been started. Generating a new UUID: c154a700-5479-11ea-993b-00163e14daa5.
2020-02-21T07:14:09.957191Z 0 [Warning] Gtid table is not ready to be used. Table 'mysql.gtid_executed' cannot be opened.
2020-02-21T07:14:10.942032Z 0 [Warning] CA certificate ca.pem is self signed.
2020-02-21T07:14:11.277977Z 1 [Note] A temporary password is generated for root@localhost: stmug/UCh1ng

```

6. 配置
修改 my.cnf,  mysql 5.7.18后已不在提供support-files/my-default.cnf 文件。

```shell
# cd /usr/local/mysql
# vim my.cnf
# chown -R mysql:mysql my.cnf 
```
my.cnf文件内容
```shell
[mysqld]
#changes to the binary log between backups.
log_bin

# These are commonly set, remove the # and set as required.
basedir = /usr/local/mysql
datadir = /opt/mysql/data
port = 3306
server_id = 100
socket = /tmp/mysqld.sock
log-error=/var/log/mysql/mysql.log
pid-file=/var/run/mysql/mysql.pid
```

注意：mysql连接localhost通常通过一个Unix域套接字文件进行，一般是/tmp/mysql.sock，这个socket路径不要修改，不然连本地mysql的时候回报错：ERROR 2002 (HY000): Can't connect to local MySQL server through socket '/tmp/mysql.sock'

因为my.cnf中配置了log-error、pid-file路径，因此需要在/var/log/、/var/run/创建mysql目录，并且给mysql用户全部的权限(chmod).否则启动会报错

```shell
chmod -R 777 /var/log/mysql/

chmod -R 777 /var/run/mysql/
```
```shell
chown -R mysql:mysql /var/log/mysql/
chown -R mysql:mysql /var/run/mysql/
```

7. 配置mysql为服务，启动mysql

```shell
# cd /usr/local/mysql
# cp -a support-files/mysql.server /etc/init.d/mysqld

```

8. 启动mysql服务

```shell
# service mysqld start
Starting MySQL.     [  OK  ]
```


9. 查看初始密码，尝试登陆mysql

```shell
# cat /root/.mysql_secret
# Password set for user 'root@localhost' at 2020-02-21 15:03:50 
pSjf<j/l:u31

# cd /usr/local/mysql
# bin/mysql -u root -p
```

问题：
Access denied for user 'root'@'localhost' (using password: YES)

解决方法：
在Linux，先进入/etc，修改my.cnf,任意一行加上"skip-grant-tables"。
```shell
# cd /usr/local/mysql
# bin/mysql -u root -p
mysql> use mysql;
update mysql.user set authentication_string=password('123qwe') where user='root' and Host = 'localhost';
```

注释掉my.cnf 
#skip-grant-tables

重启mysql
```shell
# service mysqld restart
# service mysqld status
# bin/mysql -u root -p
输入密码
登录成功

mysql>
```

遇到的问题

You must reset your password using ALTER USER statement before executing this statement.

解决方法：
 alter user 'root'@'localhost' identified by '123qwe';

 执行 sql命令
 ```shell
 mysql> show databases;

+--------------------+
| Database           |
+--------------------+
| information_schema |
| mysql              |
| performance_schema |
| sys                |
+--------------------+
4 rows in set (0.00 sec)
```

开启root远程连接
```shell
mysql> grant all on *.* to root@'%' identified by '123qwe';  
mysql> flush privileges;
````
查询，%已经添加OK;
```shell
mysql> select Host,User from user;
+-----------+---------------+
| Host      | User          |
+-----------+---------------+
| %         | root          |
| localhost | mysql.session |
| localhost | mysql.sys     |
| localhost | root          |
+-----------+---------------+
4 rows in set (0.00 sec)
```

连接成功！


修改密码为
123qwe



## 常用命令

```shell
启动命令
# service mysqld start
Redirecting to /bin/systemctl start mysqld.service

关闭命令

# service mysqld stop
Redirecting to /bin/systemctl stop mysqld.service

重启命令
# service mysqld restart
Redirecting to /bin/systemctl restart mysqld.service

查看服务状态
# service mysqld status

查看MySql系统配置
cat /etc/my.cnf

```
## 常见错误


log-error set to '/var/log/mariadb/mariadb.log', however file don't exists. Create writable for user 'mysql'.

权限问题，发现并没有/var/log/mariadb/mariadb.log这个路径,那我们就创建, 并给mysql用户授权即可


mkdir /var/log/mariadb 
touch /var/log/mariadb/mariadb.log 

chown -R mysql:mysql  /var/log/mariadb/


发生错误信息：
```shell
`2020-02-21 13:43:22 [WARNING] mysql_install_db is deprecated. Please consider switching to mysqld --initialize
2020-02-21 13:43:22 [ERROR]   Child process: /usr/local/mysql/bin/mysqldterminated prematurely with errno= 32
2020-02-21 13:43:22 [ERROR]   Failed to execute /usr/local/mysql/bin/mysqld --bootstrap --datadir=/opt/mysql/data --lc-messages-dir=/usr/local/mysql/share --lc-messages=en_US --basedir=/usr/local/mysql
-- server log begin --
/usr/local/mysql/bin/mysqld: error while loading shared libraries: libaio.so.1: cannot open shared object file: No such file or directory
```

http://www.voidcn.com/article/p-tfryonyl-mr.html


二：出错原因

mysql 5.7不支持mysql_install_db，应该使用mysqld --initialize 完成实例初始化。

三：解决办法

/usr/local/mysql/bin/mysqld --defaults-file=/usr/local/mysql/my.cnf --user=mysql --basedir=/usr/local/mysql/ --datadir=/opt/mysql/data/ --initialize

linux libaio.so.1: cannot open shared object file: No such file or directory
缺少安装包libaio和libaio-devel.
命令行
yum install libaio*。自动安装这两个包

在MySQL 5.7的初始化操作与MySQL 5.6有点不同了，下面在MySQL 5.7的版本用MySQL 5.6的初始化方式进行操作一下，让大家看下会报什么错： [WARNING] mysql_install_db is deprecated. Please consider switching to mysqld --initialize



## 连接mysql

遇到的问题
 Can't connect to local MySQL server through socket '/tmp/mysql.sock'
 原因：
 /usr/local/mysql/my.cnf中的socket写错为mysqld.sock, 多了一个d字母，应该为mysql.sock