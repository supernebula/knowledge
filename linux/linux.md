# Linux

## 1. Ubuntu 18.04 / Centos7 修改默认启动级别(图形模式、命令模式)


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

