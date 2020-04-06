---
title: 集学习娱乐开发于一身的LJ使用记录
author: Darifo
Github: 'https://github.com/darifo'
Pages: 'https://darifo.github.io/'
date: 2020-04-06 18:46:36
categories:
- 软件安装
tags:
- Docker
- 服务器
- FTP
- Win10
- MySQL
- Redis
- CentOS
---


## 前言

自己捡的垃圾，再烂也要用起来


作为一个入门级别的垃圾佬，去年自行DIY了一台小主机，最初的期望是搭建一套家庭娱乐及文件存储系统，于是乎 开干：

#### 配置

- 主板 & CPU ： 华擎 J3455 - ITX  大概四五百的样子（全新的），CPU是j3455四核四线程1.5G，板载的没法拆
- 内存 ：DDR3 1600 4G * 2  自己买一块几十块，大佬送了一块，共两块8G，可以说足够了
- 电源 ：金河田 一百多的样子（缩啥不能缩电源，其实电源卖得很便宜了）
- 硬盘 ：希捷 黑盘 500G * 2  这才是垃圾，五十块一个盘，某鱼购
- 机箱 ：杂牌ITX小机箱，不说了，就是50包邮的，某鱼

总共算下来，也差不多700多的样子。



### NAS 黑群晖

买来组装好之后，直接上的 黑群晖 ，刚开始用着很新鲜，捣鼓了好些天，功能挺强大的，基本文件存储与办公套件齐全，观影系统、下载站、照片管理、笔记本等等，最重要的是还有 **Docker** 、虚拟机啥的，这简直爽翻。

但是，由于租房的时候办理的网络宽带是移动（办卡送的），所以根本没法外网访问（移动大内网，没有公网IP，其实也可以frp、花生壳啥的做穿透、速度实在太慢）。所以只能每天回到家连上主机搞搞，没啥意思。于是，基本上放哪里吃了几个月灰。。。



### 进入正题


#### 一场事故

前些天，我重启了服务器，上群辉里面发现一个日志提示 硬盘 “堪用”，意思就是出了问题还是坏了咋的，然后就去拆机检查，是硬盘电源线松动，，再次启动，发现起不来了，让它启动了一晚上也不行，第二天就各种捣鼓，街上显示器启动查看引导、检查路由器网络接入。。。。这一查问题还真多，一边是磁盘里面数据应该被破坏了，一边又是局域网DHCP分配IP身份认证失败，反正搞了大半天没成功。。


#### 一气之下

重做引导盘，重新分区格式化磁盘

准备换成 Linux 


查了一下网上这块主板CPU相关信息，发现 官方说只支持 win10、Ubuntu，而且linux兼容不好。。。


先不管，直接上 CentOS8 ，发现还真他娘的装不上。。。放弃


#### 直接上Windows它不香吗？


安装windows还是比较顺利，

系统好了，先清理软件垃圾

关闭防火墙、软件（反正自己局域网，全部裸着好办事）

#### Windows10 和 Docker 还是比较好配合


- 首先打开 **Hyper-V** 服务

![](/images/20200405214929.png)


- 下载安装 Docker-Desktop-Windows


http://get.daocloud.io/#install-docker-for-mac-windows


![](/images/20200405215720.png)



- 启动后 配置 Docker


主要设置挂载盘、网络、镜像源

![](/images/20200406193427.png)


- 安装迅雷

略

- 打开 FTP 服务

过程详细就不说了，配置用户那里直接选用系统用户登录就行。。。

![](/images/20200406193744.png)

![](/images/20200406193847.png)

![](/images/20200406193921.png)

![](/images/20200406193947.png)

![](/images/20200406194046.png)


这里，我把 FTP  目录设置到 和 CentOS 容器绑定的目录下面，意味着，我传到ftp的文件都会同步到容器内的 CentOS 系统 目录中，方便后续做其他调试工作啥的。。。



- Docker 容器安装


0、Docker常用命令：

```shell

docker info

docker ps

docker images

docker ps -a

docker rm [容器ID]

docker rmi [镜像ID]

docker search [搜索内容]

docker pull [镜像仓库]

docker commit [要提交的容器ID] [名称:版本号]

docker run ...

docker stop [名称或容器ID]

docker start [名称或容器ID]

```



1、CentOS

```shell

docker pull centos

# 这样运行 加--privileged /usr/sbin/init 才可以 进去运行 sshd 
# /e/docker/centos 表示映射 E:/docker/centos 到 内部 /root 目录
# 一定加上 0.0.0.0 全局监听
docker run -itd --privileged --name centos_server -p 0.0.0.0:2002:22 -v /e/docker/centos:/root centos:05 /usr/sbin/init

# 一定要这样进入容器 启动 sshd
docker exec -it centos_server /bin/bash

# 进入容器后
systemctl start sshd

# 查看端口监听没有
netstat -nltp

#退出，但不停止容器
Ctrl+P+Q

```

默认是没有 openssh-Server 的，需要自行手动安装：

安装ifconfig

yum install net-tools.x86_64


安装ssh

yum -y install openssh-server

安装Vim

yum install vim


这样，，CentOS容器 + ssh 就跑起来了。连接一下试试

![](/images/20200406200730.png)

这样我就在外部其他电脑都能直接进入 Windows10 的 Docker 里面的 CentOS 系统咯，相当于有一台 linux 服务器可以随便玩。。。



2、MySQL

```shell

docker pull mysql

# 指定root密码用 -e MYSQL_ROOT_PASSWORD
docker run --name mysql_server -p 0.0.0.0:13306:3306 -v /e/docker/mysql:/data -e MYSQL_ROOT_PASSWORD=Darifo123... -d mysql

# 可进入容器
docker attach mysql_server
```


3、Redis

```shell
docker pull redis

docker run -d --name redis_server -p 0.0.0.0:16379:6379 -v /e/docker/redis:/data redis

```


其他都是一样的。。。需要时再搞




### 总结一下

- Windows10 
- FTP
- Docker
- - CentOS
- - MySQL
- - Redis
- - ...
- ...

性能比较差，先弄这么些东西跑着，等着垃圾升级，可以继续扩展，搭建一些娱乐服务。。。

甚至可以往 CentOS 部署一些爬虫，搞一些小姐姐照片啥的~ 

看下效果吧：

![](/images/20200406201845.png)
![](/images/20200406202210.png)
![](/images/20200406202236.png)
![](/images/20200406202427.png)
![](/images/20200406202531.png)
![](/images/20200406202601.png)




以上，纯属娱乐，欢迎批评指正！

