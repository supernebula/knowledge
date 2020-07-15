# Logstash

安装



调试

https://my.oschina.net/u/1538135/blog/681370

```
output {
    stdout {
        codec => rubydebug
    }
}
```

```
bin/logstash -f test_grok.conf --verbose --debug
```

需要后面使用--verbose --debug

这样直接在前台启动,便可以看到日志输出到终端.

## 最佳实践
```
vim config/pipelines.yml
```
logstash 仅stdout 标准输出
pipe管道配置
pipeline.workers: 64
pipeline.batch.size: 1000

logstash stdout + influxDB输出

   pipeline.workers: 64
   pipeline.batch.size: 1000


## Grok Debugger

https://zhuanlan.zhihu.com/p/37128731

http://grokdebug.herokuapp.com/

通过logstash filter过滤屏蔽不需要的日志

http://xiaorui.cc/archives/2538

if else  语句

https://www.elastic.co/guide/en/logstash/current/event-dependent-configuration.html#logstash-config-field-references


[ddcx-business-server-192.168.0.24-8066] 2020-07-15 12:38:24.262 INFO 20029 [http-nio-8066-exec-1] com.ddcx.interceptor.ParamsInterceptor /oauth/menu/web/tenant/permission,接口耗时:44ms

INPUT
```
[open-user-server-192.168.0.24-8066] 2020-07-15 12:38:24.262 INFO 20029 [http-nio-8066-exec-1] com.open.interceptor.ParamsInterceptor /oauth/menu/web/tenant/permission,接口耗时:44ms
```
grok
```
.%{NOTSPACE:serverName}.%{IP:clientIp}.%{INT:port}. %{TIMESTAMP_ISO8601:time} %{WORD:level} %{INT:serverPid} %{NOTSPACE} %{NOTSPACE} %{URIPATH:path},接口耗时:%{INT:time2}ms
```
结果：
```
{
  "serverName": [
    [
      "open-user-server"
    ]
  ],
  "clientIp": [
    [
      "192.168.0.24"
    ]
  ],
  "IPV6": [
    [
      null
    ]
  ],
  "IPV4": [
    [
      "192.168.0.24"
    ]
  ],
  "port": [
    [
      "8066"
    ]
  ],
  "time": [
    [
      "2020-07-15 12:38:24.262"
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
      "12",
      null
    ]
  ],
  "MINUTE": [
    [
      "38",
      null
    ]
  ],
  "SECOND": [
    [
      "24.262"
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
      "com.open.interceptor.ParamsInterceptor"
    ]
  ],
  "path": [
    [
      "/oauth/menu/web/tenant/permission"
    ]
  ],
  "time2": [
    [
      "44"
    ]
  ]
}
```
