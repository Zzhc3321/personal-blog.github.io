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

### 安装配置
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

### uwsgi常用命令

```shell
启动：
uwsgi  --ini  uwsgi.ini

停止：
uwsgi --stop uwsgi.pid

重启：
uwsgi --reload uwsgi.pid
```








<br>
<br>

***

## nginx

### 安装
```shell
添加 Nginx 源
rpm -Uvh http://nginx.org/packages/centos/7/noarch/RPMS/nginx-release-centos-7-0.el7.ngx.noarch.rpm
安装 Nginx
yum install -y nginx
启动 Nginx
systemctl start nginx.service
设置开机自启 Nginx
systemctl enable nginx.service
```


<i class="fas fa-exclamation"></i>注：nginx的配置文件在<code>/etc/nginx/nginx.conf，目录在/etc/nginx </code>

 - 自定义的配置文件放在/etc/nginx/conf.d
 - 项目文件存放在/usr/share/nginx/html/
 - 日志文件存放在/var/log/nginx/

### 配置conf.d

```shell
location /static {
            alias /home/xxx/static;
        }
location / {
            #将所有的参数转到uwsgi下
            include uwsgi_params;
            #uwsgi的ip与端口
            uwsgi_pass 127.0.0.1:8080;
        }
```







<br>
<br>

***

## 注意事项


### 常用命令


查看
ps ajx|grep uwsgi



### 关于静态文件权限