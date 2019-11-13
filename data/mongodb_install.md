# Linux Centos 7安装MongoDB

1. Linux Centos 7安装MongoDB

配置系统yum源

使用yum安装MongoDB

验证安装结果

启动MongoDB 服务

验证服务开启

远程连接Mongodb

远程连接

常用命令清单

用户权限角色说明


## yum使用详解

https://blog.csdn.net/u011305680/article/details/52767230

Install MongoDB Community Edition on Red Hat Enterprise or CentOS Linux

https://docs.mongodb.com/manual/tutorial/install-mongodb-on-red-hat/

CentOS 7上MongoDB数据库安装和卸载

https://www.linuxidc.com/Linux/2017-11/148495.htm

ubuntu mongodb远程连接配置

https://www.jianshu.com/p/03aff57dfe46

How to Enable Authentication on MongoDB

https://medium.com/mongoaudit/how-to-enable-authentication-on-mongodb-b9e8a924efac

db.createUser()

https://docs.mongodb.com/manual/reference/method/db.createUser/



```shell
// 1、开启MongoDB
sudo service mongod start  或者 systemctl start mongod.service  # 开启MongoDB
sudo chkconfig mongod on  # 加入开机启动
sudo service mongod restart # 重启MongoDB

// 2、关闭MongoDB
sudo service mongod stop  # 关闭防火墙

// 3、卸载MongoDB
sudo yum erase $(rpm -qa | grep mongodb-org)    # 卸载MongoDB
sudo rm -r /var/log/mongodb  # 删除日志文件
sudo rm -r /var/lib/mongo    # 删除数据文件



```


https://juejin.im/post/5cbe73f86fb9a0320b40d687

2. Install MongoDB Community Edition on Red Hat or CentOS

https://docs.mongodb.com/manual/tutorial/install-mongodb-on-red-hat/
