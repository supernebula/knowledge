# InfluxDB

https://www.influxdata.com/get-influxdb/

##  RPM方式 安装 （RedHat和CentOS）

```shell
wget https://dl.influxdata.com/influxdb/releases/influxdb-1.8.0.x86_64.rpm
sudo yum localinstall influxdb-1.8.0.x86_64.rpm

Running transaction
  Installing : influxdb-1.8.0-1.x86_64                                                                                                       1/1 
warning: /etc/influxdb/influxdb.conf created as /etc/influxdb/influxdb.conf.rpmnew
Created symlink from /etc/systemd/system/influxd.service to /usr/lib/systemd/system/influxdb.service.
Created symlink from /etc/systemd/system/multi-user.target.wants/influxdb.service to /usr/lib/systemd/system/influxdb.service.
  Verifying  : influxdb-1.8.0-1.x86_64     

```



## tar.gz 压缩包方式安装

Linux Binaries (64-bit)

### 下载解压

```
cd /usr/local/
wget https://dl.influxdata.com/influxdb/releases/influxdb-1.8.1_linux_amd64.tar.gz
tar xvfz influxdb-1.8.1_linux_amd64.tar.gz
cd influxdb-1.8.1-1

```

### 自定义配置数据路径 启动

```
# cd /mnt 
# mkdir influxdb

# vim /usr/local/influxdb-1.8.1-1/etc/influxdb/influxdb.conf
[meta]
dir = "/mnt/influxdb/meta"

[data]
dir = "/mnt/influxdb/data"
wal-dir = "/mnt/influxdb/wal"

# cd /etc/
# mkdir influxdb

# cd /usr/local/influxdb-1.8.1-1/etc/influxdb/
# cp influxdb.conf /etc/influxdb/influxdb.conf

# cd /usr/local/influxdb-1.8.1-1/usr/bin
# ./influxd

8888888           .d888 888                   8888888b.  888888b.
   888            d88P"  888                   888  "Y88b 888  "88b
   888            888    888                   888    888 888  .88P
   888   88888b.  888888 888 888  888 888  888 888    888 8888888K.
   888   888 "88b 888    888 888  888  Y8bd8P' 888    888 888  "Y88b
   888   888  888 888    888 888  888   X88K   888    888 888    888
   888   888  888 888    888 Y88b 888 .d8""8b. 888  .d88P 888   d88P
 8888888 888  888 888    888  "Y88888 888  888 8888888P"  8888888P"

2020-07-20T06:04:36.233449Z	info	InfluxDB starting	{"log_id": "0O6i1M2G000", "version": "1.8.1", "branch": "1.8", "commit": "af0237819ab9c5997c1c0144862dc762b9d8fc25"}
2020-07-20T06:04:36.233467Z	info	Go runtime	{"log_id": "0O6i1M2G000", "version": "go1.13.8", "maxprocs": 8}
2020-07-20T06:04:36.334759Z	info	Using data dir	{"log_id": "0O6i1M2G000", "service": "store", "path": "/mnt/influxdb/data"}

```


### 配置以服务启动

## 启动

```shell
sudo service influxdb start
```

问题：rpm  以root方式安装, 并自定义data目录，遇到的无法启动的问题

问题原因： 
默认influxdb 数据目录是 /var/lib/influxdb, 属于用户influxdb:influxdb

自定义的data目录 /mnt/influxdb ，属于root，权限问题

解决办法：
删除 /mnt/influxdb， 移动目录

```
mv /var/lib/influxdb/ /mnt/influxdb/
```

修改/etc/influxdb/influxdb.conf
```
[meta]
  # Where the metadata/raft database is stored
  dir = "/mnt/influxdb/meta"
[data]
  # The directory where the TSM storage engine stores TSM files.
  dir = "/mnt/influxdb/data"

  # The directory where the TSM storage engine stores WAL files.
  wal-dir = "/mnt/influxdb/wal"

```

```
#service influxdb start
##influx
Connected to http://localhost:8086 version 1.8.1
InfluxDB shell version: 1.8.1
> 
```
启动成功


## 启动授权

```
# influx
Connected to http://localhost:8086 version 1.8.1
InfluxDB shell version: 1.8.1
> CREATE USER admin WITH PASSWORD '123456' WITH ALL PRIVILEGES    #创建管理员用户，赋予所有权限
> CREATE USER user1 WITH PASSWORD '123456'   #创建普通用户
> GRANT WRITE ON <database> TO user1   #赋予普通用户写权限
> GRANT READ ON <database> TO user1 #赋予普通用户读权限


```






问题：启动失败

Failed to start influxdb.service: Unit not found.

points
行

CARSTA
Point由时间戳（time）：time 主键
数据（field）：AC交流电压、PW当前功率、CpVolate接口电压
标签（tags）组成：deviceNumber、road路数、transId订单号



```shell
sudo service influxdb start    #启动

```

访问测试：

```
http://127.0.0.1:8086/

```

## 集成 grafana

安装 

启动命令：

```
# service grafana-server start
```

访问

http://127.0.0.1:3000/






# logstash 集成 influxDb

Logstash 最佳实践

https://doc.yonyoucloud.com/doc/logstash-best-practice-cn/filter/grok.html




查看logstash 是否安装influxDb插件, 

```
cd /home/admin/logstash-6.8.6/bin
./logstash-plugin list --verbose | grep influx
```

安装 influxDb 插件

```
./logstash-plugin install logstash-output-influxdb
Validating logstash-output-influxdb
Installing logstash-output-influxdb
Installation successful
[admin@test1 bin]$ ./logstash-plugin list --verbose | grep influx
logstash-output-influxdb (5.0.5)
```

启动
```
# cd /usr/local/logstash-6.8.6/bin
# nohup bin/logstash -f config/logstash.conf --config.reload.automatic &
# tail -f nohup.out

Sending Logstash logs to /usr/local/logstash-6.8.6/logs which is now configured via log4j2.properties
[2020-07-09T10:15:13,295][WARN ][logstash.config.source.multilocal] Ignoring the 'pipelines.yml' file because modules or command line options are specified
[2020-07-09T10:15:13,322][INFO ][logstash.runner          ] Starting Logstash {"logstash.version"=>"6.8.6"}
[2020-07-09T10:15:23,970][INFO ][logstash.pipeline        ] Starting pipeline {:pipeline_id=>"main", "pipeline.workers"=>8, "pipeline.batch.size"=>125, "pipeline.batch.delay"=>50}
[2020-07-09T10:15:24,677][INFO ][logstash.pipeline        ] Pipeline started successfully {:pipeline_id=>"main", :thread=>"#<Thread:0x75cb17c3 sleep>"}
[2020-07-09T10:15:24,697][INFO ][logstash.inputs.tcp      ] Starting tcp input listener {:address=>"0.0.0.0:5200", :ssl_enable=>"false"}
[2020-07-09T10:15:24,790][INFO ][logstash.agent           ] Pipelines running {:count=>1, :running_pipelines=>[:main], :non_running_pipelines=>[]}
[2020-07-09T10:15:25,221][INFO ][logstash.agent           ] Successfully started Logstash API endpoint {:port=>9600}

```

## 命令行管理influxdb

https://www.jianshu.com/p/721e4ce4c066

### 登录 influxdb

```shell
# influx
# influx -username 'name1' -password 'pwd1'

Connected to http://localhost:8086 version 1.8.1
InfluxDB shell version: 1.8.1
> show databases
```

### 保存策略

#### 创建保存策略

```shell
create retention policy "rp_one_week" on "db_charge" duration 1w replication 1 default
```

#### 查看保存策略

```shell
> show retention policies on db_charge
name    duration shardGroupDuration replicaN default
----    -------- ------------------ -------- -------
autogen 0s       168h0m0s           1        true

```


