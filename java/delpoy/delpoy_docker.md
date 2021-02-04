# Docker 部署 

## Docker常用命令

查看镜像

```shell
# docker images
```

保存和载入镜像

```shell
# docker save [OPTIONS] IMAGE [IMAGE...]
# docker load
```

查看容器

```shell
# docker ps -a
```

导出和导入容器
```shell
# docker export 
# docker import
···



## 使用 Docker 部署 Spring Boot 项目

http://www.ityouknow.com/springboot/2018/03/19/spring-boot-docker.html


## windows下Springboot-Docker 打包镜像

1.  windows10安装Docker Desktop

 修改配置

(1).  Setting> General > Expose daemon on tcp://localhost:2375 without TLS

否则报错
```shell
[ERROR] Failed to execute goal com.spotify:docker-maven-plugin:1.0.0:build (default-cli) on project farm: Exception caught: java.util.concurrent.ExecutionException: com.spotify.docker.client.shaded.javax.ws.rs.ProcessingException: org.apache.http.conn.HttpHostConnectException: Connect to localhost:2375 [localhost/127.0.0.1, localhost/0:0:0:0:0:0:0:1] failed: Connection refused: connect -> [Help 1]
```

(2).  Troubleshoot > Clean/Purge data

否则报错：
```shell
docker: Error response from daemon: Ports are not available: listen tcp 0.0.0.0:80: bind: An attempt was made to access a socket in a way forbidden by its access permissions.
```

2. springboot 创建Dockerfile
简历目录：
src/docker/Dockerfile

Dockerfile内容：
```shell
FROM openjdk:8-jdk-alpine
VOLUME /tmp
ADD example-server-1.0.jar app.jar
ENTRYPOINT ["java","-Djava.security.egd=file:/dev/./urandom","-jar","/app.jar"]
```

3. 修改pom.xml 

新增内容：
```xml
    <properties>
        <docker.image.prefix>springboot</docker.image.prefix>
    </properties>

    <build>
        <plugins>
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
            </plugin>
            <!-- Docker maven plugin -->
            <plugin>
                <groupId>com.spotify</groupId>
                <artifactId>docker-maven-plugin</artifactId>
                <version>1.2.2</version>
                <configuration>
                    <imageName>${docker.image.prefix}/${project.artifactId}</imageName>
                    <dockerDirectory>src/main/docker</dockerDirectory>
                    <resources>
                        <resource>
                            <targetPath>/</targetPath>
                            <directory>${project.build.directory}</directory>
                            <include>${project.build.finalName}.jar</include>
                        </resource>
                    </resources>
                </configuration>
            </plugin>
            <!-- Docker maven plugin -->
        </plugins>
    </build>

```

3. idea  打包 springboot jar包


4. 打包 docker 

```shell
mvn package docker:build
```

5. 测试docker是否可以正常启动

```shell
docker run -p 8081:8081 -t springboot/exampe-server
```

5. 打开 Windows PowerShell
```shell
> docker images
REPOSITORY                     TAG                 IMAGE ID            CREATED             SIZE
springboot/example-server   latest              d306ecfe56e0        2 hours ago         152MB
openjdk                        8-jdk-alpine        a3562aa0b991        20 months ago       105MB
```

6. 保存镜像到本地文件 example-server.tar
```shell
> docker save springboot/exampe-server exampe-server.tar
```

7. xftp 上产到centos7

## 部署到centos7 docker

1. 前置条件
centos7 安装 docker 、openjdk8

2. 恢复文件到docker镜像

```shell
# docker load < example-server.tar
f1b5933fe4b5: Loading layer [==================================================>]  5.796MB/5.796MB
9b9b7f3d56a0: Loading layer [==================================================>]  3.584kB/3.584kB
ceaf9e1ebef5: Loading layer [==================================================>]  100.2MB/100.2MB
752bdc951812: Loading layer [==================================================>]  47.09MB/47.09MB
Loaded image: springboot/coach-user-server:latest
```

查看镜像是否恢复

```shell
# docker images
REPOSITORY                     TAG                 IMAGE ID            CREATED             SIZE
springboot/example-server      latest              d306ecfe56e0        2 hours ago         152MB
nginx                          latest              ae2feff98a0c        2 weeks ago         133MB
hello-world                    latest              bf756fb1ae65        12 months ago       13.3kB
```

启动镜像

```shell
# docker run -p 8081:8081 -t springboot/example-server
```



## 参考文档

1. Docker images保存和加载， 容器的导入和导出区别

https://www.jianshu.com/p/8408e06b7273

https://jingsam.github.io/2017/08/26/docker-save-and-docker-export.html

https://www.runoob.com/docker/docker-save-command.html

https://www.runoob.com/docker/centos-docker-install.html

http://www.ityouknow.com/docker.html

https://deepmind.t-salon.cc/article/1849

https://mp.weixin.qq.com/s/JUfxQtiTa21SFNYD7llEoA


https://www.cnblogs.com/binghe001/p/12885714.html

http://zouliming.cn/2019/04/03/docker-%E5%AF%BC%E5%85%A5%E9%95%9C%E5%83%8F%E6%97%B6%E6%8F%90%E7%A4%BAerror-processing-tar-fileexit-status-1-archivetar-invalid-tar-header/

spingboot  docker

https://juejin.cn/post/6844903970163556359

https://www.cnblogs.com/npeng/p/14267007.html


