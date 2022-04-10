---
layout: post
title: 2022-04-10-centos服务器安装mysql
tags: [数据库,mysql]
category: 服务器
toc: true
math: true
author: zzhc
---


1. 下载并安装MySQL官方的 Yum Repository

```shell
[root@localhost ~]# wget -i -c http://dev.mysql.com/get/mysql57-community-release-el7-10.noarch.rpm
```

2. 使用上面的命令就直接下载了安装用的Yum Repository，大概25KB的样子，然后就可以直接yum安装了。
```shell
[root@localhost ~]# yum -y install mysql57-community-release-el7-10.noarch.rpm
```




3. 之后就开始安装MySQL服务器。
```shell
[root@localhost ~]# yum -y install mysql-community-server
```


4. **注意** Mysql安装失败-GPG验证不通过或Failed to start mariadb.service: Unit not fou
 
```shell
yum install mysql-community-server --nogpgcheck
```