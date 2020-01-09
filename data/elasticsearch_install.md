# 安装Elasticsearch，并配置集群

https://blog.51cto.com/niubdada/2371379

## 安装JDK1.8

1. 查看当前是否安装jdk
```shell
whereis java
which java （java执行路径）
echo $JAVA_HOME
echo $PATH
```

二、卸载旧版本的JDK：

如果是卸载openjdk：直接使用
```shell
apt-get remove openjdk*
```

确定JDK的版本：
```shell
rpm -qa | grep jdk
rpm -qa | grep gcj
```

2. 下载jdk

去Oracle官网下载rpm包：

http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html


## 安装Elasticsearch

https://blog.51cto.com/niubdada/2371379

启动错误

1.  max file descriptors [4096] for elasticsearch process is too low, increase to at least [65535]

每个进程最大同时打开文件数太小，可通过下面2个命令查看当前数量

```shell
ulimit -Hn
ulimit -Sn
```
修改/etc/security/limits.conf文件，增加配置，用户退出后重新登录生效

```shell
*               soft    nofile          65536
*               hard    nofile          65536
```

解决方法：

参考： https://www.cnblogs.com/zhi-leaf/p/8484337.html

后台启动

 ```shell
$ nohup ./elasticsearch &
```
日志输出到nohup.out

查看输出

```shell
$ tail --retry -f nohup.out
```


## 安装配置Kibana

在192.168.1.11上面配置即可
下载并解压到安装目录

```shell
[root@k1 ~]# wget https://artifacts.elastic.co/downloads/kibana/kibana-6.7.2-linux-x86_64.tar.gz
[root@k1 ~]# tar -xzf kibana-6.7.0-linux-x86_64.tar.gz -C /usr/local/
```

配置Kibana

```shell
[root@k1 ~]# vim /usr/local/kibana-6.7.0-linux-x86_64/config/kibana.yml
server.host: "192.168.1.11"
elasticsearch.hosts: ["http://192.168.1.11:9200"]
```
启动Kibana

```shell
[root@k1 ~]# nohup /usr/local/kibana-6.7.0-linux-x86_64/bin/kibana &
```


但是.......,带有node 不一定就是 kibana的 进程。怎么办呢 ？ 我们知道  kibana  是 5601 对外的 tcp 端口。

 所以 使用  netstat -tunlp|grep 5601 就可以查到进程id 了 。

