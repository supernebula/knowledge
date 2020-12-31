# Linux

## 一、基本配置

### 1. Ubuntu 18.04 / Centos7 修改默认启动级别(图形模式、命令模式)


Linux分为7个启动级别：

    Runlevel 0 : 关闭系统          （系统停机状态）
    Runlevel 1 : 维护模式          （单用户工作状态）
    Runlevel 2 : 多用户状态        （没有NFS）
    Runlevel 3 : 多用户，无图形系统 （有NFS）
    Runlevel 4 : 多用户，无图形系统 （系统未使用，留给用户）
    Runlevel 5 : 多用户，图形化系统
    Runlevel 6 : 关闭并重启机器     （系统正常关闭并重新启动）

参考 

https://blog.csdn.net/weixin_42776979/article/details/81512370

https://blog.csdn.net/yjk13703623757/article/details/82808600

https://www.cnblogs.com/loading4/p/6363131.html

(1). 查看当前默认启动模式

```shell
# systemctl get-default 
```


(2). 切换为图形模式

```shell
# systemctl set-default graphical.target
或者
# systemctl set-default runlevel5.target
```

(3). 切换为文本命令模式
```shell
# systemctl set-default multi-user.target
或者
# systemctl set-default runlevel3.target
```

(4). 需要重启


切换快捷键

Ctrl+Alt+F5  文本模式   

Ctrl+Alt+F1  图形模式


# 二、网络配置

1. Ubuntu 18配置静态IP

    ubuntu server 18.04版本netplan网络配置

参考：

https://ywnz.com/linuxjc/1491.html

命令：

```shell
# cd /etc/netplan
# ls
50-cloud-init.yaml
# vim 50-cloud-init.yaml
```
修改50-cloud-init.yaml内容如下：

```yaml
# This file is generated from information provided by
# the datasource.  Changes to it will not persist across an instance.
# To disable cloud-init's network configuration capabilities, write a file
# /etc/cloud/cloud.cfg.d/99-disable-network-config.cfg with the following:
# network: {config: disabled}
network:
    ethernets:
        eth0:
            dhcp4: false
            addresses: [192.168.0.206/24]   #24 对应的子网掩码netmask是 255.255.255.0
            gateway4: 192.168.0.1
            nameservers:
                    addresses: [192.168.0.1]
    version: 2
~                       
```

应用生效新的网络配置
```shell
# netplan apply
```

