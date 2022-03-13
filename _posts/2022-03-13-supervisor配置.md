---
layout: post
title: 2022-03-13-supervisor配置 
tags: [supervisor,分布式]
category: 服务器
toc: true
math: true
author: zzhc
---


#### 1. 安装supervisor

```shell
pip3 install supervisor
```

#### 2. 初始化配置文件

```shell
#新建目录
mkdir /etc/supervisor

#配置输出到/etc/supervisor/supervisord.conf中
echo_supervisord_conf > /etc/supervisor/supervisord.conf 
```

#### 3. 配置管理进程
&emps;&emps进程管理配置参数，不建议全都写在supervisord.conf文件中，可以将每个进程写一个配置文件放在include指定的目录下包含进supervisord.conf文件中。类似nginx的配置文件。


- （1）创建/etc/supervisor/config.d目录，用于存放进程管理的配置文件。（此处目录可自定义，与配置文件中对应即可）
- （2）修改/etc/supervisor/supervisord.conf中的include参数，将/etc/supervisor/conf.d目录添加到include中。
  
  


```shell
[include]
files = /etc/supervisor/config.d/*.ini
```


#### 4. 服务配置模板

```shell
[unix_http_server]
file=/tmp/supervisor.sock   ;UNIX socket 文件，supervisorctl 会使用
;chmod=0700                 ;socket文件的mode，默认是0700
;chown=nobody:nogroup       ;socket文件的owner，格式：uid:gid

[inet_http_server]         ;HTTP服务器，提供web管理界面
port=127.0.0.1:9001        ;Web管理后台运行的IP和端口，如果开放到公网，需要注意安全性
;username=user              ;登录管理后台的用户名
;password=123               ;登录管理后台的密码

[supervisord]
logfile=/tmp/supervisord.log ;日志文件，默认是 $CWD/supervisord.log
logfile_maxbytes=50MB        ;日志文件大小，超出会rotate，默认 50MB，如果设成0，表示不限制大小
logfile_backups=10           ;日志文件保留备份数量默认10，设为0表示不备份
loglevel=info                ;日志级别，默认info，其它: debug,warn,trace
pidfile=/tmp/supervisord.pid ;pid 文件
nodaemon=false               ;是否在前台启动，默认是false，即以 daemon 的方式启动
minfds=1024                  ;可以打开的文件描述符的最小值，默认 1024
minprocs=200                 ;可以打开的进程数的最小值，默认 200

[supervisorctl]
serverurl=unix:///tmp/supervisor.sock ;通过UNIX socket连接supervisord，路径与unix_http_server部分的file一致
;serverurl=http://127.0.0.1:9001 ; 通过HTTP的方式连接supervisord

;包含其它配置文件
[include]
files = /etc/supervisor/config.d/*.ini    ;可以指定一个或多个以.ini结束的配置文件
```

#### 5. supervisor命令
```python
#启动supervisor，-c制定让其读取的配置文件
supervisord -c /etc/supervisor/supervisord.conf

#关闭supervisor
supervisorctl shutdown

#重新加载supervisor配置文件，并重启superivisor
supervisorctl reload

#添加或删除配置文件 
#更新
supervisorctl update

#管理supervisor的服务
###启动服务
supervisorctl start all
supervisorctl start service_name
###关闭服务
supervisorctl stop all
supervisorctl stop service_name
###查看状态
supervisorctl status [service_name]
###重新启动所有服务或者是某个服务
supervisorctl restart all
supervisorctl restart service_name
```

***

<br>
<br>