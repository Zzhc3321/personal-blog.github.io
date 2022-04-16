---
layout: post
title: 2022-04-16-Django项目部署至centos云服务器 
tags: [部署,服务器,centos]
category: Backend
toc: true
math: true
author: zzhc
---


## django 项目文件

### 修改setting.py
```shell
DEBUG = False
ALLOW_HOSTS=['*',]
```

### 服务器安装python依赖包

将项目文件放入服务器/home目录下
```shell
pip freeze > plist.txt

pip3 install -r plist.txt
```

测试部署环境
```shell
python manage.py check --deploy
```



<br>
<br>

***

## uwsgi

### 安装
```shell
pip3 install uwsgi
```

uwsgi.ini：

```shell
[uwsgi]
socket=127.0.0.1:8080

#项目目录
chdir=/home/xxx

#项目中wsgi.py文件的目录，相对于项目目录
wsgi-file=xxx/wsgi.py
processes=4
threads=2
master=True
pidfile=uwsgi.pid
daemonize=uwsgi.log
```

 uwsgi.ini
启动：

uwsgi  --ini  uwsgi.ini
停止：

uwsgi --stop uwsgi.pid
重启：（当然是启动的时候重启啦啊）

uwsgi --reload uwsgi.pid






python3 manage.py check --deploy










 setsebool -P httpd_can_network_connect 1











卸载nginx
首先输入命令 ps -ef | grep nginx检查一下nginx服务是否在运行。如果在运行就停止运行，需要在nginx的安装目录下的sbin执行，如果配置了环境就不需要了：
./nginx -s stop
查找、删除Nginx相关文件
查看Nginx相关文件：
whereis nginx

在这里插入图片描述
find查找相关文件
find / -name nginx

在这里插入图片描述
依次删除find查找到的所有目录：
rm -rf /usr/local/nginx  /usr/local/sbin/nginx /usr/local/nginx-1.13.9/objs/nginx
再使用yum清理
yum remove nginx
安装nginx
添加 Nginx 源
rpm -Uvh http://nginx.org/packages/centos/7/noarch/RPMS/nginx-release-centos-7-0.el7.ngx.noarch.rpm
安装 Nginx
yum install -y nginx
启动 Nginx
systemctl start nginx.service
设置开机自启 Nginx
systemctl enable nginx.service
nginx的配置文件在/etc/nginx/nginx.conf，目录在/etc/nginx

在这里插入图片描述


在这里插入图片描述
自定义的配置文件放在/etc/nginx/conf.d
项目文件存放在/usr/share/nginx/html/
日志文件存放在/var/log/nginx/
还有一些其他的安装文件都在/etc/nginx
安装完成之后就可以访问：
