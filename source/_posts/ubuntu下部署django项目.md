---
title: ubuntu下部署django项目
date: 2018-07-16 11:51:34
tags:
  - django
  - python
  - django部署
categories: django
---


参考[简书](http://www.jianshu.com/)作者Gevin 的文章 [基于nginx和uWSGI在Ubuntu上部署Django](http://www.jianshu.com/p/e6ff4a28ab5a)

#### 一 前期准备
1. nginx
```
安装
sudo apt-get install nginx
启动、停止和重启
sudo /etc/init.d/nginx start
sudo /etc/init.d/nginx stop
sudo /etc/init.d/nginx restart
或者

sudo service nginx start
sudo service nginx stop
sudo service nginx restart
```
2 安装python的一些依赖
```
sudo apt-get install python3-pip
sudo apt-get install python3-venv
sudo apt-get install python-dev
```
3.绑定ip到blog.viking666.com

4.在/soft目录下创建一个虚拟环境(目录权限设置好)
`sudo python3 -m venv blog`
激活虚拟环境
`source /soft/blog/bin/activate`   
装一些包
```
pip install django==1.8
pip install uwsgi
```

5.在/www下新建一个django项目(目录权限设置好)
`django-admin startproject myblog `
<!--more-->
#### 二. 基于uWSGI和nginx部署Django
##### 1.原理    
`the web client <-> the web server(nginx) <-> the socket <-> uwsgi <-> Django`
##### 2.基本测试
测试uWSGI是否正常
在myblog项目的根目录下创建test.py(/www/myblog/test.py)文件，添加源码如下：
```
# test.py
def application(env, start_response):
    start_response('200 OK', [('Content-Type','text/html')])
    # return ["Hello World"] # python2
    return [b"Hello World"] # python3
```
然后，Run uWSGI:

`uwsgi --http :8000 --wsgi-file test.py`   
参数含义:
- http :8000: 使用http协议，8000端口
- wsgi-file test.py: 加载指定文件 test.py

打开下面url，浏览器上应该显示hello world
`blog.viking666.com:8000`    
如果显示正确，说明下面3个环节是通畅的：

`the web client <-> uWSGI <-> Python`

测试Django项目是否正常
首先确保project本身是正常的：

`python manage.py runserver 0.0.0.0:8000`
如果没问题，使用uWSGI把project拉起来：

`uwsgi --http :8000 --module myblog.wsgi`
- module myblog.wsgi: 加载wsgi module

如果project能够正常被拉起，说明以下环节是通的：

`the web client <-> uWSGI <-> Django`

#### 三.配置nginx
安装nginx完成后，如果能正常打开`blog.viking666.com`，说明下面环节是通畅的：

`the web client <-> the web server`

增加nginx配置

将`uwsgi_params`文件拷贝到项目文件夹下(/www/myblog)。`uwsgi_params`文件在`/etc/nginx/`目录下，
在项目文件夹下创建文件夹下创建logm目录 并在里面创建`myblog.conf`(`/www/myblog/log/myblog.conf`),填入并修改下面内容：
```
upstream django_myblog {
    server unix:///www/myblog/log/myblog.sock; # for a file socket
    # server 127.0.0.1:8001; # for a web port socket (we'll use this first)
}

server {
       listen 80;

       server_name blog.viking666.com;
       charset     utf-8;
       # max upload size
       client_max_body_size 75M;   # adjust to taste
       access_log /www/myblog/log/nginx_access.log;
       error_log /www/myblog/log/nginx_error.log;
       location /media  {
            alias /www/myblog/media/;
        }

        location /static {
            alias /www/myblog/static/;
        }
       # Finally, send all non-media requests to the Django server.
       location / {
           uwsgi_pass  django_myblog;
           include     /www/myblog/uwsgi_params; # the uwsgi_params file you installed
    }

}

```
这个configuration文件告诉nginx从文件系统中拉起media和static文件作为服务，同时相应django的request

在`/etc/nginx/sites-enabled`目录下创建本文件的连接，使`nginx`能够使用它：
```
sudo ln -s /www/myblog/log/myblog.conf /etc/nginx/sites-enabled/
```
部署static文件
在django的setting文件中，添加下面一行内容：

`STATIC_ROOT = os.path.join(BASE_DIR, "static/")`
然后运行：

`python manage.py collectstatic`

测试nginx
首先重启nginx服务：

`sudo /etc/init.d/nginx restart`

然后检查media文件是否已经正常拉起：
在目录`/www/myblog/media` directory下添加文件meida.png，然后访问`blog.viking666.com:8000/media/media.png` ，成功后进行下一步测试。

#### 四.nginx and uWSGI and test.py
执行下面代码测试能否让`nginx`在页面上显示`hello, world`
```
uwsgi --socket :8001 --wsgi-file test.py
```
访问blog.viking666.com:8000 ,如果显示`hello world`，则下面环节是否通畅:
```
the web client <-> the web server <-> the socket <-> uWSGI <-> Python
```
用`UNIX socket`取代`TCP port`
对`myblog.conf`做如下修改：
```
server unix:///path/to/your/mysite/mysite.sock; # for a file socket
# server 127.0.0.1:8001; # for a web port socket (we'll use this first)
```
重启`nginx`，并在此运行`uWSGI`
```
uwsgi --socket mysite.sock --wsgi-file test.py
```
打开 `blog.viking666.com:8000` ，看看是否成功

##### 如果没有成功:
检查 nginx error
`log(/var/log/nginx/error.log)`。如果错误如下：
```
connect() to unix:///path/to/your/mysite/mysite.sock failed (13: Permission
denied)
```
添加socket权限再次运行：
```
uwsgi --socket mysite.sock --wsgi-file test.py --chmod-socket=666 # (very permissive)
```
or
```
uwsgi --socket mysite.sock --wsgi-file test.py --chmod-socket=664 # (more sensible)
```

#### 五.Running the Django application with uswgi and nginx
如果上面一切都显示正常，则下面命令可以拉起django application
```
uwsgi --socket mysite.sock --module mysite.wsgi --chmod-socket=664
Configuring uWSGI to run with a .ini file
```
每次都运行上面命令拉起django application实在麻烦，使用.ini文件能简化工作，方法如下：

在log目录下创建文件myblog.ini，填入并修改下面内容：
```
[uwsgi]
vhost = false
# plugins = python
socket=/www/myblog/log/myblog.sock
chmod-socket = 666
enable-threads = true
master=true
processes = 2
workers=5 
harakiri=30
limit-as 128
max-requests=10000
daemonize=/www/myblog/log/myblog.log
pidfile=/www/myblog/log/myblog.pid
wsgi-file=/www/myblog/myblog/wsgi.py
virtualenv=/soft/blog
chdir=/www/myblog
# clear environment on exit
vacuum= true
```
现在，只要执行以下命令，就能够拉起django application：
```
uwsgi --ini myblog.ini # the --ini option is used to specify a file
```
Make uWSGI startup when the system boots
编辑文件`/etc/rc.local`, 添加下面内容到这行代码之前`exit 0`:
```
/usr/local/bin/uwsgi --socket /path/to/mysite.sock --module /path/to/mysite.wsgi --chmod-socket=666
```

uWSGI的更多配置
```
env = DJANGO_SETTINGS_MODULE=mysite.settings # set an environment variable
pidfile = /tmp/project-master.pid # create a pidfile
harakiri = 20 # respawn processes taking more than 20 seconds
limit-as = 128 # limit the project to 128 MB
max-requests = 5000 # respawn processes after serving 5000 requests
daemonize = /var/log/uwsgi/yourproject.log # background the process & log
```
