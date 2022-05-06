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

### 3. 修改supervisor配置文件(一般不修改)

```shell
...         
[supervisord]
logfile=/tmp/supervisord.log ; main log file; default $CWD/supervisord.log
logfile_maxbytes=50MB        ; max main logfile bytes b4 rotation; default 50MB
logfile_backups=10           ; # of main logfile backups; 0 means none, default 10
loglevel=info                ; log level; default info; others: debug,warn,trace
pidfile=/tmp/supervisord.pid ; supervisord pidfile; default supervisord.pid
nodaemon=false               ; start in foreground if true; default false
silent=false                 ; no logs to stdout if true; default false
minfds=1024                  ; min. avail startup file descriptors; default 1024
minprocs=200                 ; min. avail process descriptors;default 200
;umask=022                   ; process file creation umask; default 022
;user=supervisord            ; setuid to this UNIX account at startup; recommended if root
;identifier=supervisor       ; supervisord identifier, default is 'supervisor'
;directory=/tmp              ; default is not to cd during start
;nocleanup=true              ; don't clean up tempfiles at start; default false
;childlogdir=/tmp            ; 'AUTO' child log dir, default $TEMP
;environment=KEY="value"     ; key value pairs to add to environment
;strip_ansi=false            ; strip ansi escape codes in logs; def. false

; The rpcinterface:supervisor section must remain in the config file for
; RPC (supervisorctl/web interface) to work.  Additional interfaces may be
; added by defining them in separate [rpcinterface:x] sections.
...
```


### 4. 进程配置
#### 为celery worker配置进程
在conf目录下创建supervisor_celery_worker.ini文件
```shell
[program:ice-celery-worker]
command=python manage.py celery worker -l INFO   ;启动命令
director=/root/cel/celery_demo                   ;指定工作目录，即manage.py所在的目录
environment=PATH="/root/.local/share/virtualenvs/cel-l64ISTw0/bin/" ;指定环境目录
stdout_logfile=/root/cel/celery_demo/logs/celery.worker.log    ;指定日志文件
stdeer_logfile=/root/cel/celery_demo/logs/celery.worker.log    ;错误日志文件
autostart=true                 ;自动启动
autorestart=true               ;自动重启
startsecs=10
stopwaitsecs=60
priority=998                   ;优先级
```


#### 指定配置文件启动supervisor

```shell
supervisord -c conf/supervisord.conf 
```

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