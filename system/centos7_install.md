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


4. 配置DNS域名服务器

https://blog.csdn.net/didi7696/article/details/83780933

Linux Centos7添加DNS
https://www.cnblogs.com/baihuitestsoftware/articles/9519724.html

方法一：直接命令修改
显示当前网络连接
nmcli connection show

修改当前网络连接对应的DNS服务器，这里的网络连接可以用名称或者UUID来标识，二个NDS根据需求修改，一般添加这二个就可以。
nmcli con mod ens160 ipv4.dns "114.114.114.114 8.8.8.8"
将dns配置生效
nmcli con up ens160
```shell
# nmcli connection show
NAME    UUID                                  TYPE            DEVICE 
ens192  0bd6ac7c-a5aa-4aef-ad6d-2a62949be537  802-3-ethernet  ens160 

# nmcli con mod ens160 ipv4.dns "114.114.114.114 8.8.8.8"
# nmcli con up ens160
Connection successfully activated (D-Bus active path: /org/freedesktop/NetworkManager/ActiveConnection/2)
# ping www.baidu.com
PING www.a.shifen.com (220.181.111.188) 56(84) bytes of data.
64 bytes from 220.181.111.188 (220.181.111.188): icmp_seq=1 ttl=52 time=2.13 ms
```
