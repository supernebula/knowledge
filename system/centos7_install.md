# 安装centos7

镜像
CentOS-7-x86_64-Minimal-1708.iso

## 安装

## 配置

1. 配置网卡自启动

https://blog.csdn.net/qq_27328865/article/details/91435604

```shell
# cd /etc/sysconfig/network-scripts/
# vi vi ifcfg-enoXXX

ONBOOT=yes

# systemctl restart network   重启网络服务
```



2. 安装ifconfig

https://www.linuxidc.com/Linux/2018-10/154766.htm

```shell
# yum search ifconfig
# yum install net-tools.x86_64 
```
安装后，ifconfig 命令可以正常执行

3. 配置静态IP
