---
layout: post
title: 2022-03-13-windows debug环境下运行celery worker
tags: [服务器,celery]
category: 服务器
toc: true
math: true
author: zzhc
---

### 一.收到任务但不执行任务


#### 解决办法，启动的时候，使用eventlet 方式，

```shll
pip install eventlet 

celery -A celery_tasks.main worker -l info -P eventlet  -c 10

-c是协程的数量，生产环境可以用1000
```

原因：celery不支持在windows下运行任务，需要借助eventlet来完成……

Unable to run tasks under Windows



### 二. celery task函数返回值

 