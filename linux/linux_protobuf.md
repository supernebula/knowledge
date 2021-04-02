# protobuf

## 1. centos 安装 protobuf

```shell
# yum install autoconf automake libtool curl make g++ unzip
# yum install gcc-c++

# cd /home/download

# wget -c https://github.com/protocolbuffers/protobuf/releases/download/v3.15.6/protobuf-all-3.15.6.tar.gz

# tar zvxf protobuf-all-3.15.6.tar.gz

# cd /home/download/protobuf-3.15.6
# ./configure –prefix=/usr/local/
# make  #要编译很久
# make check
# make install

```

参考链接：

https://www.cnblogs.com/fnlingnzb-learner/p/10534038.html

### 安装 windows版protobuf



### windows 环境生成C#

生成C#类的user.proto文件

```proto
option csharp_namespace = "Evol.Abp.Web.Site.Domain";
package Evol.Abp.Web.Site.Domain;

message MessageUserLogin{
    string access_token = 1;
    string username = 2;
}
```

```shell
protoc user.proto --csharp_out=D:\source\github\spring-coach\coach-common\coach-common-protobuf\src\main\java\com\evol\protobuf\domain --proto_path=D:\source\github\spring-coach\coach-common\coach-common-protobuf\src\main\java\com\evol\protobuf
```

### 生成java
生成java类的user.proto文件

```proto
syntax = "proto3";

option java_package = "com.evol.protobuf.domain";
option java_outer_classname = "MessageUser";

message MessageUserLogin {
    string access_token = 1;
    string username = 2;
}

```

```shell
protoc --java_out=./ com\evol\protobuf\user.proto
```

参考链接

https://cloud.tencent.com/developer/article/1504965

http://www.justdojava.com/2019/06/12/java-protobuf/

https://www.nuget.org/packages/protobuf-net

https://www.icode9.com/content-1-869628.html







