# InfluxDB

https://www.influxdata.com/get-influxdb/

## 安装 （RedHat和CentOS）

```shell
wget https://dl.influxdata.com/influxdb/releases/influxdb-1.8.0.x86_64.rpm
sudo yum localinstall influxdb-1.8.0.x86_64.rpm
```

## 启动

```shell
sudo service influxdb start
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
sudo service influxdb start
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


