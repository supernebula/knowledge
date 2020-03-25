参考地址：

https://www.jianshu.com/p/400b4516b98e


安装java

java sdk

https://www.oracle.com/java/technologies/javase/javase-jdk8-downloads.html

```shell
# wget https://download.oracle.com/otn/java/jdk/8u241-b07/1f5b5a70bf22433b84d0e960903adac8/jdk-8u241-linux-x64.tar.gz?AuthParam=1583757038_a696fcb8148ffa2f73b6088dff67760c
```
创建安装目录
```shell
# mkdir /usr/local/java/
```

解压至安装目录
```shell
# tar -zxvf jdk-8u171-linux-x64.tar.gz -C /usr/local/java/
```
设置环境变量
```shell
# vim /etc/profile
```
在末尾添加
```shell
export JAVA_HOME=/usr/local/java/jdk1.8.0_241
export JRE_HOME=${JAVA_HOME}/jre
export CLASSPATH=.:${JAVA_HOME}/lib:${JRE_HOME}/lib
export PATH=${JAVA_HOME}/bin:$PATH
```

使环境变量生效
```shell
# source /etc/profile
```

添加软链接
```shell
# ln -s /usr/local/java/jdk1.8.0_241/bin/java /usr/bin/java
```


检查
```shell
# java -version
java version "1.8.0_241"
Java(TM) SE Runtime Environment (build 1.8.0_241-b07)
Java HotSpot(TM) 64-Bit Server VM (build 25.241-b07, mixed mode)

```


安装 jenkins

https://pkg.jenkins.io/redhat-stable/

```shell
sudo wget -O /etc/yum.repos.d/jenkins.repo https://pkg.jenkins.io/redhat-stable/jenkins.repo
sudo rpm --import https://pkg.jenkins.io/redhat-stable/jenkins.io.key
```

```shell
yum install jenkins
```

启动Jenkins

```shell
service jenkins start
```
停止
```shell
service jenkins stop
```
重启
```shell
service jenkins restart
```
查看状态
```shell
service jenkins status
```




临时关闭防火墙，允许8080端口访问

查看jenkins 初始密码
```shell
cat /var/lib/jenkins/secrets/initialAdminPassword
```

修改Jenkins端口号

编辑文件/etc/sysconfig/jenkins
```shell
# vim /etc/sysconfig/jenkins

JENKINS_PORT="8000"
```



配置 SSH

添加用户和用户组，并设置用户密码

```shell
# groupadd admin
# useradd -g admin admin    //useradd -g [group] [user]
# passwd admin
```

查看用户和用户组

```shell
# /etc/passwd
# /etc/group
```

用户组列表文件：


https://www.jianshu.com/p/400b4516b98e


配置JDK

git 安装：

https://blog.csdn.net/xl_lx/article/details/80688556

```shell
# yum -y install git
# git --version
# git version 1.8.3.1
```

安装maven
Git plugin(Git 源码管理插件)
Maven Integration plugin(maven 打包插件)
Publish Over SSH(远程访问的SSH插件)

https://www.jianshu.com/p/79f4001809f5


创建MAVEN项目，构建，自动部署到 /home/admin

admin登录，给.sh文件增加执行权限
```shell
chmod u+x start.sh
chmod u+x stop.sh
chmod u+x restart.sh
```

```shell
$ ./start.sh

```

u代表所有者，x代表执行权限。 + 表示增加权限。
chmod u+x file.sh 就表示对当前目录下的file.sh文件的所有者增加可执行权限