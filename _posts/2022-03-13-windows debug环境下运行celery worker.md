---
layout: post
title: 2022-03-13-windows debug环境下运行celery worker
tags: [服务器,celery]
category: 服务器
toc: true
math: true
author: zzhc
---
celery正常启动后能收到任务但不执行任务的解决办法
今天一天都在处理celery的异常问题，心力交瘁……

问题一：Django 1.11开发中用celery 4 报错ValueError: not enough values to unpack (expected 3, got 0)

问题二：celery正常启动后能收到任务但不执行任务的解决办法，也没有错误提示……

网上很多人问，却没有一个解决办法，只能自己尝试各种方法，终于找到答案……，最后发现两个问题最后都是一个答案，我都遇到了是因为我换了一次电脑，真是奇葩

1、先看我的celery服务正常启动没问



2、celery可以收到任务，有提示信息



3. 解决办法，启动的时候，使用eventlet 方式，

pip install eventlet 

celery -A celery_tasks.main worker -l info -P eventlet  -c 10

##-c是协程的数量，生产环境可以用1000

原因：celery不支持在windows下运行任务，需要借助eventlet来完成……

Unable to run tasks under Windows

4、成功后的图片，心情终于轻松了……





 