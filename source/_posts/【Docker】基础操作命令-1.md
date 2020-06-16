---
title: 【Docker】基础操作命令(1)
author: Darifo
Github: 'https://github.com/darifo'
Pages: 'https://darifo.github.io/'
date: 2020-06-15 22:09:53
categories:
- Docker
tags:
- Docker
---



### 查看帮助

```shell
docker help # 查看全部帮助
docker help ps # 查看ps命令帮助
```



### 查看安装信息

```shell
docker info 	# 基础信息
docker version	# 版本信息
```



### 查看镜像

```shell
docker images
	-a	# 本地所有镜像
	--digests	# 显示摘要信息
	-f	# 过滤
	--no-trunc	# 显示完整镜像信息
	-q	# 只显示镜像ID
```



### 查看容器

```shell
docker ps	# 运行中容器
	-a	# 所有容器，包含未运行的
	-f	# 根据条件过滤
	-l	# 显示最近创建
	-n	# 显示最近创建的n个容器
	-q	# 只显示容器编号
	-s	# 显示总的文件大小
	
```



### 拉取镜像

```shell
docker pull [镜像ID]:[版本号]  
# 注：
# 1.如果本地有自动从本地缓存获取 
# 2.本地没有从远程中央仓库获取 
# 3.如果给的镜像ID包含私服地址，就去私服拉取

# 例子：
docker pull centos  # 不带版本标签，自动拉去最新版 lasted
docker pull centos:7.5 # 拉取cenos7.5版本镜像
docker pull myresp.cn/darifo/centos-dev:2.0 # 从 myresp.cn 私服拉取镜像 

```



### 删除镜像

```shell
docker rmi 
	-f	# 强制删除
	
	镜像ID或名称
```



### 运行容器

```shell
docker run 
	-it # 交互模式运行，为容器重新分配一个伪输入终端
	-d 	# 后台运行容器，返回容器ID
	-P	# （大写P）随机端口映射，内部容器端口映射到宿主机的高端口
	-p 	# （小写p）指定端口映射 宿主机端口:容器端口 可指定多组映射
	-e 	# 设置环境变量，容器中可以使用 比如：MYSQL_ROOT_PASSWORD=123456 设置mysql容器root密码
	-v 	# 挂载目录卷到容器 宿主机目录:容器目录 可挂载多个目录
	-a 	# 登录容器，必须是 -d 运行的容器
	-w 	# 指定容器工作目录 即我登陆进去默认在哪个目录
	--name 	# 为运行容器指定一个名字
	--volume-from	# 给容器挂载其他容器的卷，挂载到当前运行容器中,实现共享数据卷
	--env-file	# 指定环境变量配置文件
	--expose	# 指定容器暴露的端口，可以指定范围，一般不可被宿主机访问
	--link	# 指定容器的关联
	--net	# 容器网络设置
	--privileged	# 指定容器是否为特权容器 默认false 等于true拥有全部权限
	--restart	# 容器停止后重启策略 "no"退出不重启 "always"退出时总是重启 "on-failure"异常退出时重启
	--rm	# 容器停止后自动删除
	
	容器ID:标签
```



### 容器执行命令

```shell
docker exec
	-d	# 分离模式: 在后台运行
	-i	# 即使没有附加也保持STDIN 打开
	-t	# 分配一个伪终端
	
	容器ID或名称
# 例如
docker exec -it centos /bin/sh ~/a.sh	# 执行脚本文件
docker exec -it centos /bin/bash	# 打开终端
```



### 启动停止重启容器

```shell
docker start|stop|restart 容器ID或名称
```



### 删除容器

```shell
docker rm   
	-f	# 强制停止后删除
	-l	# 移除容器间网络连接
	-v	# 移除关联的数据卷
	
	容器ID或名称
```



### 暂停恢复容器运行

```shell
docker pause|unpause 容器ID或名称
```



### 获取容器|镜像的元数据

```shell
docker inspect 容器ID/名称|镜像ID/名称
	-s	# 显示总文件大小
	--type	# 为指定类型返回JSON
```

