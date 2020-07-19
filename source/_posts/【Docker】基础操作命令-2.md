---
title: 【Docker】基础操作命令(2)
author: Darifo
Github: 'https://github.com/darifo'
Pages: 'https://darifo.github.io/'
date: 2020-06-17 12:25:43
categories:
- Docker
tags:
- Docker
---





### 查看容器内进程信息

```shell
docker top 容器ID或名称
	ps # 支持ps命令参数显示筛选
```



### 连接到运行中容器

```shell
docker attach 
	--sig-proxy	# 参数=false 保证control+C不会关闭容器
	
	容器ID或名称
```



### 获取容器日志

```shell
docker logs
	-f	# 跟踪日志输出
	--since	# 显示某个时间开始的所有日志
	-t	# 显示时间戳
	--tail	# 列出最新n条日志
	
	容器ID或者名称
```

### 

### 容器归档

```shell
docker export 
	-o	# 将内容写出到文件
	
	容器ID或名称
	
# 输出为tar文件
# 例子：
docker export -o centos7.tar mycentos
```



### 列出容器端口映射

```shell
docker port 容器ID或名称
```



### 标记镜像

```shell
docker tag 源镜像[:标签] 目标镜像[:标签]	
# 目标镜像可包含地址和作者信息，例如：myresp.cn/darifo/imgname:2.0
```



### 从容器新建镜像

```shell
docker commit
	-a	# 提交者
	-m	# 提交信息
	-p	# 提交时是否暂停容器，默认true
	
	目标镜像[:标签]
```



### 容器数据拷贝

```shell
docker cp
	-l	# 保留源链接

# 拷贝宿主机文件到容器内部
docker cp 宿主机源文件路径 容器ID或名称:容器内部路径

# 拷贝容器内部文件到宿主机
docker cp 容器ID或名称:容器内部路径 宿主机路径

# 拷贝文件夹 -R 
```



### 查看容器文件结构变更

```shell
docker diff 容器ID或名称
```



### 查找镜像

```shell
docker search 
	-s	# 列出收藏数不少于n的镜像
	
	搜索名
```



### 查看镜像历史

```shell
docker history 
	-H	# 以可读方式显示镜像大小和日期
	--no-trunc	# 显示完整的提交记录
	-q	# 仅显示记录ID
	
	镜像名称[:标签]
```



### 镜像归档

```shell
docker save 
	-o	# 输出到文件	
	
	镜像[:标签]
	
# 例子
docker save -o centos20.tar darifo/centos:2.0
```



### 导入归档镜像

```shell
docker load
	-i	# 导入指定tar文件
	-q	# 精简输出信息
```



### 从归档文件创建镜像

```shell
docker import
	-m	# 提交信息
	
	导入的文件
	镜像[:标签]
```



