# Ubuntu 18.04

1. Ubuntu 18.04 修改默认启动级别(图形模式、命令模式)

参考 https://blog.csdn.net/weixin_42776979/article/details/81512370

(1). 切换为图形模式

```shell
# systemctl set-default graphical.target
或者
# systemctl set-default runlevel5.target
```

(2). 切换为文本命令模式
```shell
# systemctl set-default multi-user.target
或者
# systemctl set-default runlevel3.target
```

(3). 需要重启

补充：

    Runlevel 0 : 关闭系统
    Runlevel 1 : 维护模式
    Runlevel 3 : 多用户，无图形系统
    Runlevel 4 : 多用户，无图形系统
    Runlevel 5 : 多用户，图形化系统
    Runlevel 6 : 关闭并重启机器

切换快捷键

Ctrl+Alt+F5  文本模式   

Ctrl+Alt+F1  图形模式
