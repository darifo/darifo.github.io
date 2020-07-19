---
title: 【Docker】发布镜像到远程仓库
author: Darifo
Github: 'https://github.com/darifo'
Pages: 'https://darifo.github.io/'
date: 2020-06-17 21:18:45
categories:
- Docker
tags:
- Docker
---





## 需要先注册阿里云开发者账号

```
https://www.aliyun.com/
```



然后搜索 “容器云服务”，创建自己的镜像仓库



## 1. 登录阿里云Docker Registry

```shell
sudo docker login --username=xxxxxxx registry.cn-chengdu.aliyuncs.com
```

用于登录的用户名为阿里云账号全名，密码为开通服务时设置的密码。

您可以在访问凭证页面修改凭证密码。

## 2. 从Registry中拉取镜像

```shell
sudo docker pull registry.cn-chengdu.aliyuncs.com/darifo/php-dev:[镜像版本号]
```

## 3. 将镜像推送到Registry

```shell
# 先给本地镜像打标签 
sudo docker tag [ImageId] registry.cn-chengdu.aliyuncs.com/darifo/php-dev:[镜像版本号]

# 推送到仓库
sudo docker push registry.cn-chengdu.aliyuncs.com/darifo/php-dev:[镜像版本号]
```

请根据实际镜像信息替换示例中的[ImageId]和[镜像版本号]参数。



## 4.本地运行容器

```
docker run -itd --name myphpdev -p 18080:80 -p 19090:9000 -v /root/darifo/vol:/data/vol --privileged=true [镜像ID] /sbin/init
```

