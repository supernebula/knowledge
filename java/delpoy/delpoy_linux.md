# Delpoy

## FTP

### vsftpd 服务端安装

操作系统： Centos

1  检查是否安装vsftpd

```shell
# rpm -qa | grep vsftpd
```

2  没有就安装vsftpd

```shell
# yum -y install vsftpd
```

3  设置开机启动

```shell
# chkconfig vsftpd on
```

4  修改配置

注释掉/etc/vsftpd/ftpusers文件中的root，因为这一行禁止root登录

然后启动vsftpd服务

```shell
# systemctl start vsftpd
```

5：连接vsftpd服务，选用的客户端是FileZilla

### 客户端安装

下载

https://www.filezilla.cn/download

以下介绍只是适用于用ftp在物理机和虚拟机之间传输文件使用的，不适合于生产环境做服务器使用。

![filezilla client](img/filezilla_client.png)

![filezilla client](img/filezilla_client_screenshot.png)

## Maven命令打包

导航到项目pom.xml文件所在的目录，执行打包命令。

```shell
mvn clean package -Dmaven.test.skip=true
```
mvn 选项说明：

    -DskipTests，不执行测试用例，但编译测试用例类生成相应的class文件至target/test-classes下。

    -Dmaven.test.skip=true，不执行测试用例，也不编译测试用例类。


    mvn clean install -e -U


### 部署到linux

切换到 /home目录，并创建站点文件夹website

```shell
# mkdir website
# cd website/
```

使用FileZilla上床jar包到website 目录，使用java -jar xxxx.jar运行站点。

```shell
# cd website/
# ls
sample-website-0.0.1-SNAPSHOT.jar
# java -jar sample-website-0.0.1-SNAPSHOT.jar 


  .   ____          _            __ _ _
 /\\ / ___'_ __ _ _(_)_ __  __ _ \ \ \ \
( ( )\___ | '_ | '_| | '_ \/ _` | \ \ \ \
 \\/  ___)| |_)| | | | | || (_| |  ) ) ) )
  '  |____| .__|_| |_|_| |_\__, | / / / /
 =========|_|==============|___/=/_/_/_/
 :: Spring Boot ::       (v1.5.19.RELEASE)
 2019-03-18 22:57:04.130  INFO 6130 --- [           main] o.s.j.e.a.AnnotationMBeanExporter        : Registering beans for JMX exposure on startup
2019-03-18 22:57:04.163  INFO 6130 --- [           main] s.b.c.e.t.TomcatEmbeddedServletContainer : Tomcat started on port(s): 8080 (http)
2019-03-18 22:57:04.167  INFO 6130 --- [           main] c.evol.sample.SampleWebsiteApplication   : Started SampleWebsiteApplication in 4.964 seconds (JVM running for 5.532)
```

访问

```shell
# curl localhost:8080
<!DOCTYPE html>

<html lang="zh-CN">
<head>
    <meta charset="utf-8" />
    <meta http-equiv="X-UA-Compatible" content="IE=edge" />
    <title>Essingle | Log in</title>
</head>
<body>
<div>
    <p>1</p>
    <p>zhangsan</p>
    <p>zhangsan@qq.com</p>
</div>
</body>
</html>
```

或使用IP从外部访问，前提防火墙增加端口 或 关闭防火墙

```shell
# curl http://10.211.55.5:8080
<!DOCTYPE html>
<html lang="zh-CN">
<head>
    <meta charset="utf-8" />
    <meta http-equiv="X-UA-Compatible" content="IE=edge" />
    <title>Essingle | Log in</title>
</head>
<body>
<div>
    <p>1</p>
    <p>zhangsan</p>
    <p>zhangsan@qq.com</p>
</div>
</body>
</html>
```


### 部署到docker

### centos防火墙

CentOS 7.0默认使用的是firewall作为防火墙

1.查看防火墙状态

```shell
# firewall-cmd --state
```
2.停止firewall

```shell
# systemctl stop firewalld.service
```
3.禁止firewall开机启动

```shell
# systemctl disable firewalld.service
```

## Docker

### Guides  for Centos7+

#### 1. GET

Get Docker CE for Ubuntu  
https://docs.docker.com/install/linux/docker-ce/ubuntu/

Get Docker CE for CentOS    
https://docs.docker.com/install/linux/docker-ce/centos/

To install Docker CE, you need a maintained version of CentOS 7.

#### 2. Uninstall old versions

```shell
$ sudo yum remove docker docker-client docker-client-latest docker-common docker-latest docker-latest-logrotate docker-logrotate docker-engine
```

#### 3. Install using the repository

1. SET UP THE REPOSITORY

```shell
$ sudo yum install -y yum-utils device-mapper-persistent-data lvm2
```

2. 

```shell
$ sudo yum-config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo
```

#### 4. INSTALL DOCKER CE

安装最新版
```shell
$ sudo yum install docker-ce docker-ce-cli containerd.io
```
列出所有docker有效版本

```shell
$ yum list docker-ce --showduplicates | sort -r
```

安装指定版本
```shell
$ sudo yum install docker-ce-<VERSION_STRING> docker-ce-cli-<VERSION_STRING> containerd.io
```


启动Docker
```shell
$ sudo systemctl start docker
```

验证docker安装，运行 'hellp-world' 镜像
```shell
$ sudo docker run hello-world
```





## Docker Issue

按照官方指南安装daocker，运行hello-world是报错，按照以下文章操作，并重启centos后，报错，运行成功；

Centos 7 如何卸载docker

https://blog.csdn.net/liujingqiu/article/details/74783780

## 使用 Docker 部署 Spring Boot

### 上传服务器，再创建docker镜像

1. 本地maven打包成jar
```shell
mvn clean package -Dmaven.test.skip=true
```
2. 上传jar 和 Dockerfile 到linux服务器同一目录

```shell
# pwd
/home/website
# ls
Dockerfile  sample-website-0.0.1-SNAPSHOT.jar
```

3. 构建docker镜像

```shell
# docker build -t samplesite:0.01 .
Sending build context to Docker daemon  26.82MB
Step 1/4 : FROM openjdk:8-jdk-alpine
 ---> e9ea51023687
Step 2/4 : VOLUME /tmp
 ---> Running in 3d471b970e98
Removing intermediate container 3d471b970e98
 ---> 4086a495875e
Step 3/4 : COPY sample-website-0.0.1-SNAPSHOT.jar app.jar
 ---> f22feafa6c58
Step 4/4 : ENTRYPOINT ["java","-Djava.security.egd=file:/dev/./urandom","-jar","/app.jar"]
 ---> Running in 0b616dffe734
Removing intermediate container 0b616dffe734
 ---> cbb2f9423914
Successfully built cbb2f9423914
Successfully tagged samplesite:0.01
```
查看镜像
```shell
# docker image ls
REPOSITORY      TAG             IMAGE ID        CREATED         SIZE
samplewebsite   v0.01           9214aff225b5    6 minutes ago   132MB
openjdk         8-jdk-alpine    e9ea51023687    12 days ago     105MB
hello-world     latest          fce289e99eb9    2 months ago    1.84kB
```
4. 构建docker容器

命令格式

    docker run [OPTIONS] IMAGE [COMMAND] [ARG...]

```shell
# docker run -d -p 8080:8080 samplesite:0.01
# docker run -d -p 8080:8080 --name samplesite1  samplesite:0.01


```

查看容器

```shell
# docker ps -a
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS                    NAMES
8634a0b613ef        samplesite:0.01     "java -Djava.securit…"   3 seconds ago       Up 2 seconds        0.0.0.0:8080->8080/tcp   samplesite1
```

访问容器服务(此处为web站点)
```shell
# curl http://localhost:8080/
<!DOCTYPE html>

<html lang="zh-CN">
<head>
    <meta charset="utf-8" />
    <meta http-equiv="X-UA-Compatible" content="IE=edge" />
    <title>Essingle | Log in</title>
</head>
<body>
<div>
    <p>1</p>
    <p>zhangsan</p>
    <p>zhangsan@qq.com</p>
</div>
</body>
</html>
```


参考

查看容器日志
```shell
docker logs [options] 容器id
```

#### Issue

(1). 没有端口号
```shell
# docker ps -a
CONTAINER ID        IMAGE                 COMMAND                  CREATED             STATUS              PORTS               NAMES
29245e793222        samplewebsite:v0.01   "java -Djava.securit…"   7 seconds ago       Up 6 seconds                            samplesite1
```
问题原因：

运行docker run 命令时格式错误，正确的命令格式 

    docker run [OPTIONS] IMAGE [COMMAND] [ARG...]

参考：

https://www.cnblogs.com/yi1036943655/p/9879464.html

### 开发机构建docker镜像，并上传到服务器

参考：

1. Spring Boot with Docker

    https://spring.io/guides/gs/spring-boot-docker/

2. 使用 Docker 部署 Spring Boot

    http://www.ityouknow.com/springboot/2018/03/19/spring-boot-docker.html

## 本地打包到docker

```shell
# mvn package docker:build
```

报错1:

```shell
[ERROR] No plugin found for prefix 'docker' in the current project and in the plugin groups [org.apache.maven.plugins, org.codehaus.mojo] available from the repositories [local (/Users/evol/.m2/repository), central (https://repo.maven.apache.org/maven2)] 
```

解决1：

在 .m2文件夹下，创建settings.xml, 内容如下：
```xml
    <settings xmlns="http://maven.apache.org/SETTINGS/1.0.0"
      xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
      xsi:schemaLocation="http://maven.apache.org/SETTINGS/1.0.0
                          https://maven.apache.org/xsd/settings-1.0.0.xsd">
    <pluginGroups>
        <pluginGroup>com.spotify</pluginGroup>
    </pluginGroups>
</settings>
```

参考: 
https://github.com/spotify/docker-maven-plugin/issues/322

再次执行 
```shell
# mvn package docker:build
```

报错2：
```shell
[INFO] BUILD FAILURE
[INFO] ------------------------------------------------------------------------
[INFO] Total time:  02:58 min
[INFO] Finished at: 2019-03-20T16:06:21+08:00
[INFO] ------------------------------------------------------------------------
[ERROR] Failed to execute goal com.spotify:docker-maven-plugin:1.2.0:build (default-cli) on project sample-domain: Exception caught: Must specify baseImage if dockerDirectory is null -> [Help 1]
[ERROR] 
[ERROR] To see the full stack trace of the errors, re-run Maven with the -e switch.
[ERROR] Re-run Maven using the -X switch to enable full debug logging.
[ERROR] 
[ERROR] For more information about the errors and possible solutions, please read the following articles:
[ERROR] [Help 1] http://cwiki.apache.org/confluence/display/MAVEN/MojoExecutionException
[ERROR] 
[ERROR] After correcting the problems, you can resume the build with the command
[ERROR]   mvn <goals> -rf :sample-domain
```


## Centos 7

设置开机自动联网

修改ifcg-xxx文件，xxx代表centos有线网络的名称,替换这个xxx
（注：不是“ifcg-io”文件）

```shell
# vim /etc/sysconfig/network-scripts/ifcg-xxx
```
修改DNBOOT

```shell
DNBOOT=yes
```




##






 



