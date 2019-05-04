# linux配置natapp

## NATAPP官网

https://natapp.cn/

## 教程/文档

https://natapp.cn/article

## 下载

https://natapp.cn/#download


服务器发行版 Ubuntu18.04 

## 参考

https://github.com/natapp/natapp_autostart/blob/master/systemd/natapp.service



## 第一步：安装natapp

natapp_linux_amd64_2_3_9.zip

1. 创建目录/usr/local/natapp
```shell
# cd /usr/local/
# mkdir natapp
```

2. 复制到/usr/local/natapp目录下

```shell
# cp natapp_linux_amd64_2_3_9.zip /usr/local/natapp/natapp_linux_amd64_2_3_9.zip
```

3. 解压

```shell
# cd /usr/local/natapp
# unzip natapp_linux_amd64_2_3_9.zip
```

4. 编写启动脚本natapp_start.sh

```shell
# cd /usr/local/natapp
# vim natapp_start.sh
```
脚本：

```
#!/bin/sh                                                #
/usr/local/natapp/natapp -authtoken=xxx -log=stdout      #xxx替换为真是natapp authtoken
``` 

5. 修改权限

```shell
# cd /usr/local/natapp
# chmod 777 natapp              #赋予可执行权限
# chmod 777 natapp_start.sh     #赋予读写执行权限
```

6. 测试启动

```shell
# cd /usr/local/natapp
# ./natapp_start.sh
```




## 第二步骤：配置natapp.service 自启动服务

 1. 创建 natapp.service配置

 ```shell
# cd /lib/systemd/system
# vim natapp.service
 ```

 natapp.service内容：

 ```shell
# Centos 7 / Ubuntu18.04
# 存放位置 /usr/lib/systemd/system
# 开启 systemctl start natapp
# 关闭 systemctl stop natapp
# 刷新启动脚本 
# 开机启动 systemctl enable natapp
# 取消开机启动 system disable natapp

[Unit]
#简单介绍当前服务单元
Description=NatApp Service
#Wants=network-online.target
#需要哪些服务单元启动后启动当前服务单元
After=network.target

[Service]
Type=simple
#告诉 systemd 如何启动服务。启动的服务应当为持续运行的服务，不要加后台运行的选项
ExecStart=/usr/local/natapp/natapp_start.sh   
#这是本文解决异常退出自动重启的方法，只要简单加一行
Restart=always                                

[Install]
WantedBy=multi-user.target
#Alias=natapp.service

 ```

 2. 删除natapp.service执行权限

 ```shell
# cd /lib/systemd/system
# chmod -x  natapp.service
 ```

 3. 创建软连接到

 命令格式： ln -s 源文件 目标文件

 ```shell
# cd /etc/systemd/system
# ln -s /lib/systemd/system/natapp.service /etc/systemd/system/natapp.service
 ```

 4. 刷新配置
 
 刚刚配置的服务需要让systemctl能识别

 ```shell
# systemctl daemon-reload
 ```

 5. systemd 配置服务开机自启动

 ```shell
# systemctl enable nginx.service
 ```

 6. 重启服务器测试

 ```shell
# shutdown -r now
 ```

 7. natapp配置SSH外网访问

隧道类型：TCP

外部端口：3022

外部域名：xxx.natapp.cc

阿里云CNAME： xxx.evol.ltd

内部地址：127.0.0.1

内部端口：22

终端访问

```shell
$ ssh -p 3022 root@xxx.evol.ltd
The authenticity of host '[dell.evol.pub]:3022 ([47.97.153.27]:3022)' can't be established.
ECDSA key fingerprint is SHA256:wB0Fw4dvEVTELQdiaTqi2jab12rS1ql/m5T2nKWLydc.
Are you sure you want to continue connecting (yes/no)? yes

Warning: Permanently added '[xxx.evol.pub]:3022' (ECDSA) to the list of known hosts.
root@dell.evol.pub's password: 

Welcome to Ubuntu 18.04.2 LTS (GNU/Linux 4.18.0-17-generic x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/advantage

 * Ubuntu's Kubernetes 1.14 distributions can bypass Docker and use containerd
   directly, see https://bit.ly/ubuntu-containerd or try it now with

     snap install microk8s --classic

 * Canonical Livepatch is available for installation.
   - Reduce system reboots and improve kernel security. Activate at:
     https://ubuntu.com/livepatch

146 个可升级软件包。
0 个安全更新。

Your Hardware Enablement Stack (HWE) is supported until April 2023.
Last login: Sat May  4 12:32:49 2019 from 127.0.0.1
root@ubuntu1:~# 
```


## ISSUE

1. 启动失败，拒绝外网SSH连接
排除原因：
参看系统日志
系统日志

搜索：natapp

消息：
Configuration file /lib/systemd/system/natapp.service is marked executable. Please remove executable permission bits. Proceeding anyway.

优先级：
4

删除可执行权限
chmod -x natapp.service
不改变a.txt原有的其他属性。可以是rw-r--r--
chmod 666 强制改成rw-rw-rw- 

全部日志
搜索：natapp

日志内容：
发送方：systemd
消息：Started NatApp Service.
优先级：6
主题：natapp.service单元已结束启动
定义由：systemd
支持：http://www.ubuntu.com/support
natapp.service单元已结束启动
启动结果为“RESULT”。


重点：
#告诉 systemd 如何启动服务。启动的服务应当为持续运行的服务，不要加后台运行的选项

https://www.l2b.ltd/2019/431-ubuntu18-04-systemd-%E8%AE%BE%E7%BD%AE%E6%9C%8D%E5%8A%A1%E5%BC%82%E5%B8%B8%E9%80%80%E5%87%BA%E8%87%AA%E5%8A%A8%E9%87%8D%E5%90%AF/







