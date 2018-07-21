---
title: 小飞机-Qt5配置
date: 2018-03-08 09:32:02
tags:
  - shadowsocks
  - ssr
  - fq
  - 翻墙
  - ssr服务端
  - ubuntu
  - linux
categories:
  - shadowsocks
  - ubuntu
---
参考csdn[我的Linux入门之路 - 02.Shadowsocks-Qt5配置](http://blog.csdn.net/xienaoban/article/details/54772942)

这里我使用的是[shadowsocks-Qt5版本](https://github.com/shadowsocks/shadowsocks-qt5/wiki)。毕竟是从win过渡过来的，一开始还是倾向GUI。
安装方法也异常简单，简单到我一开始不相信。（官方github上有安装文档。幸运的是，github可以直接访问）。三行代码即可解决，无需自己从github下载客户端。
首先打开终端（Ctrl+Alt+T）
然后分步运行以下三行命令。
```
sudo add-apt-repository ppa:hzwhuang/ss-qt5
sudo apt-get update
sudo apt-get install shadowsocks-qt5
```

然后就安装完成了。从你的dash（左上角相当于win开始键的东西）上能找到他，拖到任务栏上创个快捷方式就好啦。打开后如下图（File菜单里有个import from gui-config.json，把你的文件导入就行了。比别的版本的ss好的是，它能导入多个服务器，而别的版本的ss的config文件貌似只能添加一个）：
![界面](/images/小飞机-Qt5配置/界面.png)
<!--more-->
## 命令行 也可以启动客户端
sslocal -c /opt/ss.json
```
{
  "server": "*.*.*.*",
  "server_port": port_id,
  "local_port": port_id,
  "password": "*********",
  "method": "you_method"
}

```


## 配置终端代理
挂代理的东西不少，我使用的是Polipo，和shadowsocks的socks5搭配比较不错。
Polipo安装：
```
sudo apt-get install polipo
```
打开配置文件：
```
sudo nano /etc/polipo/config
```
修改配置文件：
```
# This file only needs to list configuration variables that deviate
# from the default values.  See /usr/share/doc/polipo/examples/config.sample
# and "polipo -v" for variables you can tweak and further information.

logSyslog = true
logFile = /var/log/polipo/polipo.log
socksParentProxy = "127.0.0.1:1080"
socksProxyType = socks5
logLevel=4
```

然后重启polipo：
```
sudo service polipo stop
sudo service polipo start
```

Polipo默认的代理地址是 `http_proxy=http://localhost:8123 `
那么每次对于希望 番羽 土啬 的指令，只需在前面加一句`http_proxy=http://localhost:8123` 即可。当然每次输入这么一长串这比较麻烦，可以打开～/.bashrc，在最后面添加一句
```
alias fanqiang="http_proxy=http://localhost:8123"
```

这样以后只需在需要 番羽 土啬 的指令前面加一句fanqiang即可了。
先来测试下有没有成功，输入：
```
fanqiang curl ip.gs
```

如果不想每条指令都输入fanqiang，可以
```
export http_proxy=http://localhost:8123
```

## 开机启动

命令行执行
```
gnome-session-properties
```
打开的界面就像这样

![](/images/小飞机-Qt5配置/linux添加自启动.png)
添加一个
```
Shadowsocks-Qt5
/usr/bin/ss-qt5
Shadowsocks-Qt5
```
![](/images/小飞机-Qt5配置/启动应用程序首选项.png)
