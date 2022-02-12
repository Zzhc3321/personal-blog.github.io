---
layout: post
title: 2022-02-12-利用portainer远程连接ubuntu服务器docker
tags: [docker,服务器]
category: docker
toc: true
math: true
author: zzhc
---


### 一.portainer配置

```shell
1.启动 protainer
> docker pull portainer/portainer

2.启动 protainer
>docker run -d --name portainerUI -p 9000:9000 -v /var/run/docker.sock:/var/run/docker.sock portainer/portainer

3.访问 protainer
http://你安装protainer地址:9000
```

***

<br>
<br>

### 二.服务器配置

#### 1.docker配置
```shell
1. 编辑docker.service
vim /usr/lib/systemd/system/docker.service
找到 ExecStart字段修改如下
ExecStart=/usr/bin/dockerd-current -H tcp://0.0.0.0:2375 -H unix://var/run/docker.sock 

2. 重启docker重新读取配置文件，重新启动docker服务
systemctl daemon-reload
systemctl restart docker

3. 开放防火墙端口
firewall-cmd --zone=public --add-port=2375/tcp --permanent

4.刷新防火墙
firewall-cmd --reload

5.再次配置远程docker就可以了
```

#### 2.服务器安全组配置
![enter description here](https://gitee.com/zzhc3321/personal-blog-drawing-bed/raw/master/personal-blog/2022_2_12_关联组.jpeg)

![enter description here](https://gitee.com/zzhc3321/personal-blog-drawing-bed/raw/master/personal-blog/2022_2_12_安全组.jpeg)

***

<br>
<br>

### 三.portainer连接

![enter description here](https://gitee.com/zzhc3321/personal-blog-drawing-bed/raw/master/personal-blog/2022_2_12_配置连接.jpeg)

![enter description here](https://gitee.com/zzhc3321/personal-blog-drawing-bed/raw/master/personal-blog/2022_2_12_配置连接2.jpeg)