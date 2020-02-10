# redis 安装配置

## 参考

[深入剖析Redis系列(一) - Redis入门简介与主从搭建](https://juejin.im/post/5b76e732f265da4376203849)

[Redis的三种模式：主从、哨兵、集群](http://www.pianshen.com/article/1023276171/)

[深入剖析Redis系列](https://juejin.im/post/5b76e732f265da4376203849)

[Redis5.0.5主从复制配置](https://blog.csdn.net/zhou16333/article/details/99258776)



## Redis单机安装（Mac）

1. 下载redis 5.0

http://www.redis.cn/download.html

2. 复制到 /usr/local/ 

```shell
$ sudo cp redis-5.0.5.tar /usr/local/redis-5.0.5.tar
```
3. 解压

```shell
$ sudo tar zxvf redis-5.0.5.tar 
```
4. 编译

```sehll
# sudo make test //测试编译 
# sudo make install //安装redis
```
如果在第"make test"步测试时报如下错：
Executing test client: couldn't execute "src/redis-benchmark": no such file or directory.

则执行以下两个步骤:

    (1).sudo make distclean
    (2).sudo make

5. 启动redis
```shell
$ cd /usr/local/redis-5.0.5
$ ./src/redis-server
```

### 单机配置

在redis目录下建立bin，etc，db三个目录

```shell
sudo mkdir  /usr/local/redis-5.0.5/bin
sudo mkdir  /usr/local/redis-5.0.5/etc
sudo mkdir  /usr/local/redis-5.0.5/db
```

拷贝 redis.conf 到 /usr/local/redis-5.0.5/etc下

```shell
cp /usr/local/redis-5.0.5/redis.conf /usr/local/redis-5.0.5/etc
```

修改redis.conf

vim redis.conf
```shell
#修改为守护模式
daemonize yes
#设置进程锁文件
pidfile /Users/hu/Downloads/redis/redis.pid #根据自己的路径进行相关配置
#端口
port 6379
#客户端超时时间
timeout 300
#日志级别
loglevel debug
#日志文件位置
logfile /Users/hu/Downloads/redis/log-redis.log #根据自己的路径进行相关配置
#设置数据库的数量，默认数据库为16，可以使用SELECT 命令在连接上指定数据库id
databases 16
##指定在多长时间内，有多少次更新操作，就将数据同步到数据文件，可以多个条件配合
#save
#Redis默认配置文件中提供了三个条件：
save 900 1
save 300 10
save 60 10000
#指定存储至本地数据库时是否压缩数据，默认为yes，Redis采用LZF压缩，如果为了节省CPU时间，
#可以关闭该#选项，但会导致数据库文件变的巨大
rdbcompression yes
#指定本地数据库文件名
dbfilename dump.rdb
#指定本地数据库路径
dir /Users/hu/Downloads/redis/db/ #根据自己的路径进行相关配置
#指定是否在每次更新操作后进行日志记录，Redis在默认情况下是异步的把数据写入磁盘，如果不开启，可能
#会在断电时导致一段时间内的数据丢失。因为 redis本身同步数据文件是按上面save条件来同步的，所以有
#的数据会在一段时间内只存在于内存中
appendonly no
#指定更新日志条件，共有3个可选值：
#no：表示等操作系统进行数据缓存同步到磁盘（快）
#always：表示每次更新操作后手动调用fsync()将数据写到磁盘（慢，安全）
#everysec：表示每秒同步一次（折衷，默认值）
appendfsync everysec
```

然后保存退出。

设置权限

```shell
cd /usr/local/redis-5.0.5
新建日志
touch log-redis.log
设置日志可写权限
chmod -R a+rw log-redis.log
```

启动redis
```shell
cd /usr/local/redis-5.0.5
./bin/redis-server ./etc/redis.conf
```

## Redis常用命令

1. 启动命令

$ redis-server /usr/local/redis.conf

2.  关闭命令

$ redis-cli -h 127.0.0.1 -p 6379 shutdown

3. 查看是否启动

$ ps -ef | grep redis

4. 进入客户端

$ redis-cli

5. 关闭客户端

$ redis-cli shutdown

## Redis集群


用到的命令

复制整个目录: cp -r /源目录/ /目的目录/

建立主从redis总目录

```shell
$ cd /usr/local/
$ mkdir redis-m-slave
```
分别复制单机redis到主节点、从节点

```shell
$ cp -r redis-5.0.5/ redis-m-slave/redis-master/
$ cp -r redis-5.0.5/ redis-m-slave/redis-slave/
```

建个空文件，仅供说明当前redis版本

```shell
$ touch redis-5.0.5.version
```

修改主节点配置文件

```shell
cd redis-m-slave/redis-master/
vim etc/redis.conf
```

修改端口
port 16379


修改从节点配置文件

```shell
cd redis-m-slave/redis-slave/
vim etc/redis.conf
```
修改端口
port 26379

追加内容，命令格式： slaveof [主节点IP] [主节点端口]
slaveof 127.0.0.1 16379

启动主、从节点
```shell
cd /usr/local/redis-m-slave/redis-master
./bin/redis-server ./etc/redis.conf
tail -f log-redis.log
```

```shell
cd /usr/local/redis-m-slave/redis-slave
./bin/redis-server ./etc/redis.conf
tail -f log-redis.log
```

配置完成

正常情况：
1.仅启动主节点，主节点可以连接
1.仅启动从节点，从节点可以连接

问题：
主从节点都启动时， 从节点可连接，主节点无法连接，提示授权失败













