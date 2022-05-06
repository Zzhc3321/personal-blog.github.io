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

### 3. 配置管理进程


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