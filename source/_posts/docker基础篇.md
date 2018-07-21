---
title: docker基础篇
date: 2018-03-11 15:37:27
tags:
  - docker
  - ubuntu
---

### 安装docker
ubuntu16.04是docker.io
```
sudo apt-get install docker.io
```
![apt安装docker](/images/docker基础篇/apt安装docker.png)

### 查看当前的镜像
```
# 增加 docker组
sudo groupadd docker
# 将当前用户加入docker组
sudo gpasswd -a ${USER} docker
# 重启docker服务
sudo service docker restart
# 切换当前会话到新 group
newgrp - docker
# 注意，最后一步是必须的，否则因为 groups 命令获取到的是缓存的组信息，刚添加的组信息未能生效
```
<!--more-->
### 查看docker版本
```
docker version
```
![查看docker版本](/images/docker基础篇/查看docker版本.png)

### 镜像加速
鉴于国内网络问题，后续拉取 Docker 镜像十分缓慢，我们可以需要配置加速器来解决，我使用的是网易的镜像地址：`http://hub-mirror.c.163.com`。
新版的 Docker 使用 `/etc/docker/daemon.json（Linux）` 或者 `%programdata%\docker\config\daemon.json（Windows）` 来配置 Daemon。
请在该配置文件中加入（没有该文件的话，请先建一个）：
```
{
  "registry-mirrors": ["http://hub-mirror.c.163.com"]
}
```

### 搜索ubuntu镜像
```
docker search ubuntu
```
![](/images/docker基础篇/搜索ubuntu镜像.png)


### 拉取ubuntu
```
docker pull ubuntu:16.04
```
![拉取ubuntu镜像](/images/docker基础篇/拉取ubuntu镜像.png)

### 进入ubuntu镜像
```
```

### 更新
```
apt-get update
```

### 安装该装的软件
```
apt-get install wget xz-utils ruby build-essential libssl-dev libffi-dev libjpeg-dev libfreetype6-dev zlib1g-dev libreadline6 libreadline6-dev python3-dev libmysqlclient-dev(根据需求安装mysql依赖还是sqlite依赖) libsqlite-dev libsqlite3-dev libbz2-dev -y
```

### 下载python
```
v=3.6.4
wget http://mirrors.sohu.com/python/$v/Python-$v.tar.xz
```

### 解压 trz.xz文件
```
tar xvJf Python-3.6.4.tar.xz
```

### 编译
```
./configure --enable-optimizations
make && make install
```

### 更新软连接
```
ln -s easy_install-3.6 easy_install
ln -s idle3 idle                   
ln -s pydoc3 pydoc
ln -s pip3 pip    
ln -s python3 python
ln -s python3-config python-config

```

### 给自己编辑的保存一下
```
docker commit -m="has update" -a="wsqy" e218edb10161 wsqy/ubuntu:v1
各个参数说明：

-m:提交的描述信息

-a:指定镜像作者

e218edb10161：容器ID

wsqy/ubuntu:v1:指定要创建的目标镜像名
```
docker images 命令来查看我们的新镜像 `wsqy/ubuntu:v1`：
![这是多次之后的镜像列表](/images/docker基础篇/多次之后的镜像列表.png)

### 存出镜像
```
docker save -o xxx.tar [NAME]:[TAG]
```

### 载入镜像
```
docker load --input xxx.tar
```

> tar是归档命令 没有压缩的功能 现在压缩成tar.gz
```
# tar 压缩成tar.gz
tar -zcvf xxx.tar.gz xxx.tar

# tar.gz 解压成归档文件
gunzip xxx.tar.gz
```

### 上传镜像
```
docker push [NAME]:[TAG]
```

### 运行容器
```
docker run -d -v /home/qy/Desktop/dockerTest/HelloProject:/HelloProject -w /HelloProject -p 8088:8088 wsqy/eros:v6 python manage.py runserver 0.0.0.0:8088
```
![运行容器](/images/docker基础篇/运行容器.png)

运行后将返回容器id
查看在运行中的容器
```
docker ps
```
![docker运行的进程](/images/docker基础篇/docker运行的进程.png)

看到了端口映射正确则可以访问下是否运行正常
![成功拉起django的测试页](/images/docker基础篇/成功拉起django的测试页.png)

命令详解:
-d 后台运行
-v 数据卷挂载到容器
-w 指定容器的主目录
-p 指定端口映射

### 其他的常用命令
#### 停止容器
```
docker stop CONTAINER NAMEs
# 经常需要 ctrl + c 才能停止成功
```
#### 启动容器
```
docker start CONTAINER NAMEs
```
#### 进入容器
```
docker attach CONTAINER NAMEs
```
#### 查看容器日志
```
docker logs -f CONTAINER NAMEs
```
