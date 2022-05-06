---
layout: post
title: 2022-03-13-celery 和 supervisor配置 
tags: [supervisor,分布式]
category: Backend
toc: true
math: true
author: zzhc
---

## celery(beat)配置queue和routes

1. 手动定义queue


```python
CELERY_QUEUES = (
    Queue('default', exchange=Exchange('default'), routing_key='default'),
    Queue('update_minute_data', exchange=Exchange('update_minute_data'), routing_key='update_minute_data'),
    Queue('update_daily_data', exchange=Exchange('update_daily_data'), routing_key='update_daily_data'),
)
```


2. 定义routes



```python
CELERY_ROUTES = {
    'forecast.tasks.update_daily_data': {'queue': 'update_daily_data', 'routing_key': 'update_daily_data'},
    'forecast.tasks.update_minute_data': {'queue': 'update_minute_data', 'routing_key': 'update_minute_data'},
}
```



3. 启动worker时指定该worker执行哪一个queue中的任务
  
```shell

# worker
celery -A Graduation_design worker -l info -P eventlet -n worker_csv -c 10 -Q update_minute_data

# beat
celery -A Graduation_design beat -l info --scheduler django_celery_beat.schedulers:DatabaseScheduler
```

<i class="fas fa-exclamation">注意事项</i>

时区问题：
DJANGO_CELERY_BEAT_TZ_AWARE = False


<br>
<br>

***
## supervisor管理进程

### 1. 安装supervisor

```shell
pip3 install supervisor
```

### 2. 初始化配置文件

```shell
#新建目录
mkdir /etc/supervisor

#配置输出到/etc/supervisor/supervisord.conf中
echo_supervisord_conf > /etc/supervisor/supervisord.conf 
```

### 3. 修改supervisor配置文件

```shell
[unix_http_server]
file=/tmp/supervisor.sock   ;UNIX socket 文件，supervisorctl 会使用
;chmod=0700                 ;socket文件的mode，默认是0700
;chown=nobody:nogroup       ;socket文件的owner，格式：uid:gid
 
;[inet_http_server]         ;HTTP服务器，提供web管理界面
;port=127.0.0.1:9001        ;Web管理后台运行的IP和端口，如果开放到公网，需要注意安全性
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
 
; [program:xx]是被管理的进程配置参数，xx是进程的名称
[program:xx]
command=/opt/apache-tomcat-8.0.35/bin/catalina.sh run  ; 程序启动命令
autostart=true       ; 在supervisord启动的时候也自动启动
startsecs=10         ; 启动10秒后没有异常退出，就表示进程正常启动了，默认为1秒
autorestart=true     ; 程序退出后自动重启,可选值：[unexpected,true,false]，默认为unexpected，表示进程意外杀死后才重启
startretries=3       ; 启动失败自动重试次数，默认是3
user=tomcat          ; 用哪个用户启动进程，默认是root
priority=999         ; 进程启动优先级，默认999，值小的优先启动
redirect_stderr=true ; 把stderr重定向到stdout，默认false
stdout_logfile_maxbytes=20MB  ; stdout 日志文件大小，默认50MB
stdout_logfile_backups = 20   ; stdout 日志文件备份数，默认是10
; stdout 日志文件，需要注意当指定目录不存在时无法正常启动，所以需要手动创建目录（supervisord 会自动创建日志文件）
stdout_logfile=/opt/apache-tomcat-8.0.35/logs/catalina.out
stopasgroup=false     ;默认为false,进程被杀死时，是否向这个进程组发送stop信号，包括子进程
killasgroup=false     ;默认为false，向进程组发送kill信号，包括子进程
 
;包含其它配置文件
[include]
files = relative/directory/*.ini    ;可以指定一个或多个以.ini结束的配置文件默认配置是制定*.ini，因个人习惯命名为*.conf文件


```


### 4.


### 5. supervisor命令
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