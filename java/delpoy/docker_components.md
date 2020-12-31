# 常用软件Docker部署

## mysql:5.7.31

```shell
docker pull mysql:5.7.31
docker run -d --name mysql -p 3306:3306 -e MYSQL_ROOT_PASSWORD=123456 -e TZ=Asia/Shanghai -v /root/mysql/data:/var/lib/mysql mysql:5.7.31 --default-time_zone='+8:00'
```

## redis:6.0.6

```shell
docker pull redis:6.0.6
docker run -d --name redis -p 6379:6379 -e TZ=Asia/Shanghai -v /root/redis/data:/data -v /root/redis/conf/redis.conf:/usr/local/etc/redis/redis.conf redis:6.0.6 redis-server /usr/local/etc/redis/redis.conf --appendonly yes --requirepass 123456
```

## rabbitmq:3.8.5

```shell
docker pull rabbitmq:3.8.5-management
docker run -d --hostname my-rabbitmq --name rabbitmq -p 5672:5672 -p 15672:15672 -e TZ=Asia/Shanghai -v /etc/localtime:/etc/localtime:ro -v /root/rabbitmq/data:/var/lib/rabbitmq -v /root/rabbitmq/log:/var/log/rabbitmq rabbitmq:3.8.5-management
```

## elk:780

```shell
docker pull sebp/elk:780
docker run -d --name elk -p 9200:9200 -p 5044:5044 -p 5601:5601 -e TZ=Asia/Shanghai -v /etc/localtime:/etc/localtime:ro -v /root/elk/data:/var/lib/elasticsearch sebp/elk:780

###elk 密码
./elasticsearch-setup-passwords interactive/auto
```

## mongo:4.2.8

```shell
docker pull mongo:4.2.8
docker run -d --name mongo -p 27017:27017 -e MONGO_INITDB_ROOT_USERNAME=root -e MONGO_INITDB_ROOT_PASSWORD=123456 -e TZ=Asia/Shanghai -v /etc/localtime:/etc/localtime:ro -v /root/mongo/data:/data/db mongo:4.2.8
```

## nginx:1.19.1

```shell
docker pull nginx:1.19.1
docker run -d --name nginx -p 80:80 -e TZ=Asia/Shanghai -v /etc/localtime:/etc/localtime:ro -v /root/nginx/data:/usr/share/nginx/html:ro nginx:1.19.1
```

## gitlab-ce

```shell
docker pull gitlab/gitlab-ce:rc
docker run -d --name gitlab -p 8443:443 -p 8090:80 -e TZ=Asia/Shanghai -v /etc/localtime:/etc/localtime:ro -v /root/gitlab/conf:/etc/gitlab -v /root/gitlab/data:/var/opt/gitlab -v /root/gitlab/log:/var/log/gitlab gitlab/gitlab-ce:rc
root 12345678
```

## nexus3:3.25.0

```shell
docker pull sonatype/nexus3:3.25.0
docker run -d --name nexus -p 8081:8081 -e TZ=Asia/Shanghai -v /etc/localtime:/etc/localtime:ro -v /root/nexus/data:/nexus-data -u root --privileged=true sonatype/nexus3:3.25.0
admin 123456
```

##   jenkins:2.60.3

```shell
过期（不可用）
docker pull jenkins:2.60.3
docker run -d --name jenkins -p 8082:8080 -p 50000:50000 -e TZ=Asia/Shanghai -v /etc/localtime:/etc/localtime:ro -v /root/jenkins/data:/var/jenkins_home -u root --privileged=true jenkins:2.60.3
```

## jenkinsci/blueocean:1.23.2

可用
```shell
docker pull jenkinsci/blueocean:1.23.2
docker run -d --name jenkins -p 8082:8080 -e TZ=Asia/Shanghai -v /etc/localtime:/etc/localtime:ro -v /root/jenkins/data:/var/jenkins_home -u root --privileged=true jenkinsci/blueocean:1.23.2
admin 123456


收费（需要破解）
```shell
docker pull atlassian/jira-software:8.5
docker run -d --name jira -p 8083:8080 -e TZ=Asia/Shanghai -v /etc/localtime:/etc/localtime:ro -v /root/jira/data:/var/atlassian/application-data/jira atlassian/jira-software:8.5

curl -L https://github.com/docker/compose/releases/download/1.26.2/docker-compose-`uname -s`-`uname -m` -o /usr/local/bin/docker-compose
chmod +x /usr/local/bin/docker-compose
```

## sentinel（限流-数据在内存中）
```shell
docker pull bladex/sentinel-dashboard:1.7.2
docker run -d --name sentinel -p 8858:8858 -e TZ=Asia/Shanghai -v /etc/localtime:/etc/localtime:ro bladex/sentinel-dashboard:1.7.2
java -Dserver.port=8858 -Dcsp.sentinel.dashboard.server=localhost:8858 -Dproject.name=sentinel-dashboard -jar sentinel-dashboard-1.7.0.jar
```

```shell
apt-get update
apt-get install vim
```
























