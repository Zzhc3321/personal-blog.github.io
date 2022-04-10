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

5. 启动MySQL
   
```shell
systemctl start  mysqld.service
```


6. 查看MySQL运行状态

```shell
systemctl status mysqld.service
```







7. 配置密码

```shell
grep "password" /var/log/mysqld.log

mysql -uroot -p

mysql> ALTER USER 'root'@'localhost' IDENTIFIED BY 'new password';
```

8. 开启远程访问

```shell

grant all privileges on *.* to 'root'@'%' identified by 'password' with grant option;

mysql> flush privileges; 

mysql> exit

```

9. 开启防火墙开放端口

```shell
[root@localhost ~]# firewall-cmd --zone=public --add-port=3306/tcp --permanent

# 然后再重新载入
[root@localhost ~]# firewall-cmd --reload

```

10. 更改mysql语言为utf8

```shell
首先重新登录mysql，然后输入status：

先退出mysql，然后再到、etc目录下的my.cnf文件下修改一下文件内容

character-set-server=utf8
collation-server=utf8_general_ci

```