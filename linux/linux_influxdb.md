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

