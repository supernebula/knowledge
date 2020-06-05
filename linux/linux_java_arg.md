
Java启动参数及调优

https://www.cnblogs.com/juzii/p/5973516.html



centos 7后台启动 springboot jar项目


nohup java -jar springboot-thread-0.0.1-SNAPSHOT.jar  &


nohup java -server -Xms2g -Xmx2g -Xmn512m -XX:MetaspaceSize=64m -XX:-OmitStackTraceInFastThrow -XX:+HeapDumpOnOutOfMemoryError -XX:HeapDumpPath=/home/admin/ddcx-iot-ac-server/logs/iot-ac_heapdump.hprof -jar server-1.0.jar --spring.config.location=./config/bootstrap.properties >/dev/null 2>&1 &