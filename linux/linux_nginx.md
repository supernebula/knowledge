安装nginx

https://blog.csdn.net/u011630575/article/details/48103243


https://blog.csdn.net/Jenson_/article/details/78801743


1. 下载并解压

```shell
wget -c http://nginx.org/download/nginx-1.18.0.tar.gz  #下载

cp nginx-1.18.0.tar.gz /usr/local/                     #复制

tar -zxvf nginx-1.18.0.tar.gz                         #解压



2. 执行编译

```shell
cd /usr/local/nginx-1.8.0                           #进入
./configure                                         #准备编译

make
make install
```

 如果有/usr/local/nginx文件夹，说明已经安装好了。



安装Nginx时报错

./configure: error: the HTTP rewrite module requires the PCRE library.

./configure: error: the HTTP rewrite module requires the PCRE library.

安装pcre-devel解决问题
yum -y install pcre-devel

./configure: error: the HTTP gzip module requires the zlib library.
You can either disable the module by using --without-http_gzip_module
option, or install the zlib library into the system, or build the zlib library
statically from the source with nginx by using --with-zlib=<path> option.

则需要安装“zlib-devel”即可。SSH执行以下命令：
yum install -y zlib-devel


```shell

./configure #执行编译

.............
checking for PCRE JIT support ... found
checking for zlib library ... found
creating objs/Makefile

Configuration summary
  + using system PCRE library
  + OpenSSL library is not used
  + using system zlib library

  nginx path prefix: "/usr/local/nginx"
  nginx binary file: "/usr/local/nginx/sbin/nginx"
  nginx modules path: "/usr/local/nginx/modules"
  nginx configuration prefix: "/usr/local/nginx/conf"
  nginx configuration file: "/usr/local/nginx/conf/nginx.conf"
  nginx pid file: "/usr/local/nginx/logs/nginx.pid"
  nginx error log file: "/usr/local/nginx/logs/error.log"
  nginx http access log file: "/usr/local/nginx/logs/access.log"
  nginx http client request body temporary files: "client_body_temp"
  nginx http proxy temporary files: "proxy_temp"
  nginx http fastcgi temporary files: "fastcgi_temp"
  nginx http uwsgi temporary files: "uwsgi_temp"
  nginx http scgi temporary files: "scgi_temp"

```




```shell
cd  /usr/local/nginx
cd sbin
./nginx  #启动ginx

打开浏览器：输入http://localhost

如果显示  welcome to  nginx

 ```


 centos7 配置 nginx 自启动

 https://www.jianshu.com/p/ca5ee5f7075c


 nginx永久加入到系统环境变量

 在/etc/profile 中加入：
export NGINX_HOME=/usr/local/nginx
export PATH=$PATH:$NGINX_HOME/sbin
保存，
执行 source /etc/profile ，使配置文件生效。
学习必须如蜜蜂一样，采过许多花，这才能酿出蜜来。



