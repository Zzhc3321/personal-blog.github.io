---
layout: post
title: 2022-05-04-celery beat配置 
tags: [分布式]
category: Backend
toc: true
math: true
author: zzhc
---

### 定时任务

#### 如果我们想某日某时执行某个任务，或者每隔一段时间执行某个任务，也可以使用celery来完成.   使用定时任务，需要安装额外包:
```shell
pip install django_celery_beat
```

#### 首先在settings.py中安装此应用:
```python
INSTALLED_APPS = [
    ...
    'django_celery_beat',  # 安装应用
    ...
]
```
