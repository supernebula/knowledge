
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
