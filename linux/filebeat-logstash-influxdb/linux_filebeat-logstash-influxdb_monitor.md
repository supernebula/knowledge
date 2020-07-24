# Centos7 filebeat + logstash + influxdb + grafana 打造网站日志接口性能监控系统

filebeat+logstash+influxdb+ Grafana打造网站日志监控系统

https://www.imooc.com/article/23145

## 步骤

### 服务器1 安装 logstash 、 influxdb （ESSD硬盘）


### 服务器2 部署Springboot/cloud服务、安装filebeat

部署Springboot/cloud服务，服务日志目录:

/mnt/logs/example-server/example-server-info.log
/mnt/logs/example2-server/example2-server-info.log

日志格式：

```shell
[example-server-192.168.0.10-8066] 2020-07-15 16:14:26.786 INFO 20029 [http-nio-8066-exec-1] com.example.interceptor.ParamsFilter /user/web/getUserInfo,接口耗时:19ms
```


## filebeat

### 安装filebeat
下载filebeat-6.8.6-linux-x86_64.tar.gz

```shell
cd /home/admin
wget -c http://.................../filebeat-6.8.6-linux-x86_64.tar.gz
tar -zxvf filebeat-6.8.6-linux-x86_64.tar.gz
cd filebeat-6.8.6-linux-x86_64
```

### 配置filebeat
修改默认的配置文件配置文件 filebeat.yml

```shell
$ cd /home/admin/filebeat-6.8.6-linux-x86_64
$ vim filebeat.yml

filebeat.inputs:
- type: log
  enabled: true
  paths:
    - /mnt/logs/example-server/*.log
    - /mnt/logs/example2-server/*.log

#注释到elasticsearch配置
#output.elasticsearch:
  # Array of hosts to connect to.
 # hosts: ["localhost:9200"]

#启用logstash配置
output.logstash:
  # The Logstash hosts

  hosts: ["192.168.0.2:5054"]   #logstachu所在服务器IP，端口自定义

```

### 启动filebeat

编写启动脚本，并赋予执行权限

```shell
$ cd /home/admin/filebeat-6.8.6-linux-x86_64
$ vim fb-start.sh
nohup ./filebeat -e -c filebeat.yml >/dev/null &     

$ chmod +x fb-start.sh
$ ./fb-start.sh
```

    说明 >/dev/null 加此选项不写入nohup.out文件


## logstash

### 安装logstash

下载logstash-6.8.6.tar.gz

```shell
cd /home/admin
wget -c http://.................../logstash-6.8.6.tar.gz
tar -zxvf logstash-6.8.6.tar.gz
cd logstash-6.8.6
```

### 配置logstash

新建logstash.conf， 配置input、filter、output

```shell
$ cd /home/admin/logstash-6.8.6/config
$ vim logstash.conf
#从filebeat收集服务接口日志，写入influxdb
input {
        beats {
                port => "5054"
        }
}


#过滤, 只记录接口耗时日志
filter{
        if "接口耗时" not in [message] {
                drop {}
        }

        grok{
                match => ["message", '.%{NOTSPACE:serverName}.%{IP:clientIp}.%{INT:port}. %{TIMESTAMP_ISO8601:time} %{WORD:level} %{INT:serverPid} %{NOTSPACE} %{NOTSPACE} %{URIPATH:uriPath},接口耗时:%{INT:elapsedTime}ms']
        }
}

#输出

output {
   influxdb {
        db => "data1"
        host => "127.0.0.1"
        port => "8086"
        user => "admin"
        password => "123456"
        allow_time_override => false
        flush_size => 1
        idle_flush_time => 1
        measurement => "access_audit_log"
        coerce_values => {
                "elapsed_milli" => "integer"
        }
        data_points => {
                "server_name" => "%{serverName}"
                "path" => "%{uriPath}"
                "client_ip" => "%{clientIp}"
                "elapsed_milli" => "%{elapsedTime}"
        }
        send_as_tags => ["server_name", "path", "client_ip"]

   }
   stdout {
       codec => rubydebug
   }
}

```

#### logstach filter 参考文档

1. 使用Logstash filter grok过滤日志文件

https://www.jianshu.com/p/d46b911fb83e

2. grok-patterns

https://github.com/logstash-plugins/logstash-patterns-core/blob/master/patterns/grok-patterns#L8

3. Grok Debugger

http://grokdebug.herokuapp.com/


修改 pipelines.yml, 修改线程数和批处理数量

（根据批处理写入IO性能和服务器CPU数量权衡worker数量，根据并发吞吐量权衡批处理数量）

```shell
$ cd /home/admin/logstash-6.8.6/config
$ vim pipelines.yml
pipeline.workers: 32
pipeline.batch.size: 200
```

### 启动 logstash

编写启动脚本，并赋予执行权限

调试模式的启动脚本，打印日志到nohup.out

```shell
$ cd /home/admin/logstash-6.8.6
$ vim start.debug.sh
nohup bin/logstash -f config/logstash.conf --config.reload.automatic --verbose --debug &
 

$ chmod +x start.debug.sh
$ ./start.debug.sh
```

正式启动脚本，不打印日志

```shell
$ cd /home/admin/logstash-6.8.6
$ vim start.sh
nohup bin/logstash -f config/logstash.conf --config.reload.automatic >/dev/null &
 
$ chmod +x start.sh
$ ./start.sh
```


## 附录：Grok Debugger

http://grokdebug.herokuapp.com/

日志正文

```shell
[example-server-192.168.0.10-8066] 2020-07-15 16:14:26.786 INFO 20029 [http-nio-8066-exec-1] com.example.interceptor.ParamsFilter /user/web/getUserInfo,接口耗时:19ms
```

grok表达式

```shell
.%{NOTSPACE:serverName}.%{IP:clientIp}.%{INT:port}. %{TIMESTAMP_ISO8601:time} %{WORD:level} %{INT:serverPid} %{NOTSPACE} %{NOTSPACE} %{URIPATH:uriPath},接口耗时:%{INT:elapsedTime}ms
```

解析结果：

```json
{
  "serverName": [
    [
      "example-server"
    ]
  ],
  "clientIp": [
    [
      "192.168.0.10"
    ]
  ],
  "IPV6": [
    [
      null
    ]
  ],
  "IPV4": [
    [
      "192.168.0.10"
    ]
  ],
  "port": [
    [
      "8066"
    ]
  ],
  "time": [
    [
      "2020-07-15 16:14:26.786"
    ]
  ],
  "YEAR": [
    [
      "2020"
    ]
  ],
  "MONTHNUM": [
    [
      "07"
    ]
  ],
  "MONTHDAY": [
    [
      "15"
    ]
  ],
  "HOUR": [
    [
      "16",
      null
    ]
  ],
  "MINUTE": [
    [
      "14",
      null
    ]
  ],
  "SECOND": [
    [
      "26.786"
    ]
  ],
  "ISO8601_TIMEZONE": [
    [
      null
    ]
  ],
  "level": [
    [
      "INFO"
    ]
  ],
  "serverPid": [
    [
      "20029"
    ]
  ],
  "NOTSPACE": [
    [
      "[http-nio-8066-exec-1]",
      "com.example.interceptor.ParamsFilter"
    ]
  ],
  "uriPath": [
    [
      "/user/web/getUserInfo"
    ]
  ],
  "elapsedTime": [
    [
      "19"
    ]
  ]
}
```

## 参考文章

logstash中grok使用if判断消息是否包含某个字符串

http://p1htmlkernalweb.mybluemix.net/articles/logstash%E4%B8%ADgrok%E4%BD%BF%E7%94%A8if%E5%88%A4%E6%96%AD%E6%B6%88%E6%81%AF%E6%98%AF%E5%90%A6%E5%8C%85%E5%90%AB%E6%9F%90%E4%B8%AA%E5%AD%97%E7%AC%A6%E4%B8%B2_1637870_csdn.html

Grok 正则捕获

https://doc.yonyoucloud.com/doc/logstash-best-practice-cn/filter/grok.html













