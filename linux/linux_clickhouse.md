# Clickhouse 

## CentOS or RedHat 安装

### 在线安装 

```
sudo yum install yum-utils
sudo rpm --import https://repo.clickhouse.tech/CLICKHOUSE-KEY.GPG
sudo yum-config-manager --add-repo https://repo.clickhouse.tech/rpm/clickhouse.repo
sudo yum install clickhouse-server clickhouse-client

sudo /etc/init.d/clickhouse-server start
clickhouse-client
```

启动
```
# /etc/init.d/clickhouse-server start
Start clickhouse-server service: Path to data directory in /etc/clickhouse-server/config.xml: /var/lib/clickhouse/
DONE

```

### 手动安装

在线安装很慢，先下载rpm包

https://my.oschina.net/u/4312036/blog/3222611



### 卸载

https://blog.csdn.net/daletxt/article/details/104524177/

下载地址：

https://repo.yandex.ru/clickhouse/rpm/stable/x86_64/

clickhouse-client-20.5.4.40-2.noarch.rpm
clickhouse-server-20.5.4.40-2.noarch.rpm
clickhouse-common-static-20.5.4.40-2.x86_64.rpm

```shell
cd /root/download/clickhose
[root@clickhouse]# ll
total 119300
-rw-r--r-- 1 root root     76151 Aug  5 13:02 clickhouse-client-20.5.4.40-2.noarch.rpm
-rw-r--r-- 1 root root 121976643 Aug  5 13:03 clickhouse-common-static-20.5.4.40-2.x86_64.rpm
-rw-r--r-- 1 root root     98616 Aug  5 13:02 clickhouse-server-20.5.4.40-2.noarch.rpm

# rpm -ivh clickhouse-common-static-20.5.4.40-2.x86_64.rpm 
Preparing...                          ################################# [100%]
Updating / installing...
   1:clickhouse-common-static-20.5.4.4################################# [100%]
# rpm -ivh clickhouse-client-20.5.4.40-2.noarch.rpm 
Preparing...                          ################################# [100%]
Updating / installing...
   1:clickhouse-client-20.5.4.40-2    ################################# [100%]
   # rpm -ivh clickhouse-server-20.5.4.40-2.noarch.rpm 
Preparing...                          ################################# [100%]
Updating / installing...
   1:clickhouse-server-20.5.4.40-2    ################################# [100%]
Path to data directory in /etc/clickhouse-server/config.xml: /var/lib/clickhouse/


# cp -r /var/lib/clickhouse/ /mnt/clickhouse/
# chown -R clickhouse:clickhouse /mnt/clickhouse/

```

#### 修改数据目录到指定/mnt/clickhouse/

```shell
#vim /etc/clickhouse-server/config.xml

    <!-- Path to data directory, with trailing slash. -->
    <path>/mnt/clickhouse/</path>

    <!-- Path to temporary data for processing hard queries. -->
    <tmp_path>/mnt/clickhouse/tmp/</tmp_path>
```



## 配置


1. 允许外网连接

DBeaver 客户端连接 clickhouse Connection refused: connect

```shell
 cd /etc/clickhouse-server/
 cp config.xml config.xml.bak
 vim config.xml

<!--<listen_host>::</listen_host>-->  #把注释掉的<listen_host>::</listen_host>取消注释，然后重启服务：

如果无法启动，改成如下：
<listen_host>0.0.0.0</listen_host>

service clickhouse-server restart 
```


## 客户端工具 DataGrip

DataGrip

https://www.jetbrains.com/datagrip/

## 使用一： 基本使用

### ClickHouse Tutorial 使用指南

https://clickhouse.tech/docs/en/getting-started/tutorial/


### 数据类型

https://help.aliyun.com/document_detail/146006.html?spm=a2c4g.11186623.6.591.5eee34d0UXaOXU

### 创建表

```shell
CREATE TABLE orders
(
    id Int32 DEFAULT 0,
    tenant_id Int32 DEFAULT 0,
    site_id Int32 DEFAULT 0,
    device_id Int32 DEFAULT 0,
    order_no String DEFAULT 0,
    user_name Nullable(String),
    create_time DateTime
)ENGINE = MergeTree()
PRIMARY KEY (intHash32(id))
ORDER BY (intHash32(id),tenant_id)
SAMPLE BY intHash32(id)
SETTINGS index_granularity= 8192 ;
```

### 插入数据

```shell
INSERT INTO orders (id, tenant_id, site_id, device_id, order_no, user_name, create_time)
VALUES (1, 1, 1, 1, 'CDb234223424242424', '123455555', '2020-06-30 12:00:08')
```


### 查询 

```sql
select * from orders 
```

## myql 到 clickhouse 数据导入

1. 史上最全-mysql迁移到clickhouse的5种办法

https://cloud.tencent.com/developer/article/1469128

2. StreamSets 导入

https://anjia0532.github.io/2019/06/10/cdh-streamsets/



1. 建立外表：mysql引擎，数据表实际存储在外部mysql中

```SQL
CREATE TABLE orders_myql
(
  id Int32,
  tenant_id Int32,
  site_id Int32,
  device_id Int32,
  order_no String,
  user_name String,
  user_id Int32,
  pay_type Int32,
  device_number String,
  device_ways Int32,
  order_type Int32,
  charging_prepaid_money Int32,
  charging_estimate_amount Int32,
  device_billing_type Int32,
  device_billing_price String,
  charging_actual_money Int32,
  refund_money Int32,
  refund_time DateTime,
  charging_actual_amount Int32,
  end_time DateTime,
  parent_order_id Int32,
  device_type Int32,
  phone_model_type Int32,
  pay_source_type Int32,
  device_take_out Int32,
  is_order_cancel Int32,
  device_refund_time Int32,
  status Int32,
  online_card_number String,
  province String,
  city String,
  area String,
  err_type Int32,
  reduce_money Int32,
  reduce_type Int32,
  reduce_id Int32,
  charge_code String,
  create_time DateTime,
  update_time DateTime,
  remark String
) ENGINE = MySQL('192.168.0.2:3306', 'example_history_data', 'orders', 'user1', '*********');
```


2. 建立表 ： 无索引

```sql
CREATE TABLE orders
(
  id Int32,
  tenant_id Int32,
  site_id Int32,
  device_id Int32,
  order_no String,
  user_name Nullable(String),
  user_id Nullable(Int32),
  pay_type Int32,
  device_number String,
  device_ways Int32,
  order_type Int32,
  charging_prepaid_money Nullable(Int32),
  charging_estimate_amount Nullable(Int32),
  device_billing_type Int32,
  device_billing_price Nullable(String),
  charging_actual_money Nullable(Int32),
  refund_money Int32,
  refund_time Nullable(DateTime),
  charging_actual_amount Nullable(Int32),
  end_time Nullable(DateTime),
  parent_order_id Int32,
  device_type Int32,
  phone_model_type Nullable(Int32),
  pay_source_type Nullable(Int32),
  device_take_out Int32,
  is_order_cancel Int32,
  device_refund_time Int32,
  status Int32,
  online_card_number Nullable(String),
  province Nullable(String),
  city Nullable(String),
  area Nullable(String),
  err_type Int32,
  reduce_money Nullable(Int32),
  reduce_type Nullable(Int32),
  reduce_id Nullable(Int32),
  charge_code Nullable(String),
  create_time DateTime,
  update_time Nullable(DateTime),
  remark Nullable(String)
) ENGINE = MergeTree()
PRIMARY KEY (intHash32(id))
ORDER BY (intHash32(id),tenant_id)
SAMPLE BY intHash32(id)
SETTINGS index_granularity= 8192 ;
```


3. 从MySQL外表查询 并 插入数据到clickhouse中

```sql
INSERT INTO orders select * from mysql('192.168.0.2:3306', 'example_history_data', 'orders', 'user1', '******') WHERE id >= 1975989 AND id <  2975989

```

4. clickhouse 条件查询

```sql
SELECT COUNT(*) FROM orders WHERE parent_order_id = 0  AND create_time > toDate('2020-05-25') AND create_time < toDate('2020-07-25')
```


## 使用二： 生产使用


2. 建立表 ： 排序 索引

```sql
CREATE TABLE orders_history
(
  id Int32,
  tenant_id Int32,
  site_id Int32,
  device_id Int32,
  order_no String,
  user_name Nullable(String),
  user_id Nullable(Int32),
  pay_type Int32,
  device_number String,
  device_ways Int32,
  order_type Int32,
  charging_prepaid_money Nullable(Int32),
  charging_estimate_amount Nullable(Int32),
  device_billing_type Int32,
  device_billing_price Nullable(String),
  charging_actual_money Nullable(Int32),
  refund_money Int32,
  refund_time Nullable(DateTime),
  charging_actual_amount Nullable(Int32),
  end_time Nullable(DateTime),
  parent_order_id Int32,
  device_type Int32,
  phone_model_type Nullable(Int32),
  pay_source_type Nullable(Int32),
  device_take_out Int32,
  is_order_cancel Int32,
  device_refund_time Int32,
  status Int32,
  online_card_number Nullable(String),
  province Nullable(String),
  city Nullable(String),
  area Nullable(String),
  err_type Int32,
  reduce_money Nullable(Int32),
  reduce_type Nullable(Int32),
  reduce_id Nullable(Int32),
  charge_code Nullable(String),
  create_time DateTime,
  update_time Nullable(DateTime),
  remark Nullable(String)
) ENGINE = MergeTree()
PARTITION BY toYYYYMM(create_time)
ORDER BY (id, create_time)
SETTINGS index_granularity= 8192;
```


