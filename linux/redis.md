# redis 安装配置

## 单机redis (Mac)

### 安装

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

### 配置

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

## Redis集群

