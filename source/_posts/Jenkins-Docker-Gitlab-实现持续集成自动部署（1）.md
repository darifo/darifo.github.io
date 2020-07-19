---
title: Jenkins + Docker + Gitlab 实现持续集成自动部署（1）
author: Darifo
Github: 'https://github.com/darifo'
Pages: 'https://darifo.github.io/'
date: 2020-07-19 11:58:15
categories:
    - 环境安装
tags:
    - Jenkins
    - Docker
    - Gitlab
    - 自动化部署
    - CI/CD
---

## 迈出持续集成与发布第一步


目的： 实现代码自动部署与多分支多环境发布


前置条件：

- linux服务器（centos7+）

- docker 环境 （注意最新版本）

- git 环境 （注意更新版本）

  


废话不多说，直接开干，，，

首先我有一台linux服务器，装的操作系统是 centos7.5 内核版本（3.10.0-862.el7.x86_64）



### 升级安装docker

然后咱们装新 docker 

如果原来装了docker 就直接升级，否则就从第3步安装开始




1、停止docker服务

```
systemctl stop docker

rpm -qa | grep docker

```


2、卸载当前安装的版本（注意：操作卸载不会清除之前创建的容器和拉取的镜像，放心！）

```
yum erase docker \
                  docker-client \
                  docker-client-latest \
                  docker-common \
                  docker-latest \
                  docker-latest-logrotate \
                  docker-logrotate \
                  docker-selinux \
                  docker-engine-selinux \
                  docker-engine \
                  docker-ce
```


3、开始使用yum安装依赖

```
yum install -y yum-utils  device-mapper-persistent-data lvm2
```

4、配置仓库

```
yum-config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo
```

5、查看仓库里面可以安装的docker版本

```
yum list docker-ce --showduplicates | sort -r
```

6、安装docker

```
yum install docker-ce -y
```

7、启动服务

```
systemctl start docker
```

8、查看验证版本

```
docker version
```

```
Client: Docker Engine - Community
 Version:           19.03.12
 API version:       1.40
 Go version:        go1.13.10
 Git commit:        48a66213fe
 Built:             Mon Jun 22 15:46:54 2020
 OS/Arch:           linux/amd64
 Experimental:      false

Server: Docker Engine - Community
 Engine:
  Version:          19.03.12
  API version:      1.40 (minimum version 1.12)
  Go version:       go1.13.10
  Git commit:       48a66213fe
  Built:            Mon Jun 22 15:45:28 2020
  OS/Arch:          linux/amd64
  Experimental:     false
 containerd:
  Version:          1.2.13
  GitCommit:        7ad184331fa3e55e52b890ea95e65ba581ae3429
 runc:
  Version:          1.0.0-rc10
  GitCommit:        dc9208a3303feef5b3839f4323d9beb36df0a9dd
 docker-init:
  Version:          0.18.0
  GitCommit:        fec3683
```



升级Docker后重启容器出现错误Unknown runtime specified docker-runc



```
grep -rl 'docker-runc' /var/lib/docker/containers/ | xargs sed -i 's/docker-runc/runc/g'
```



```
systemctl stop docker
```



```
systemctl start docker
```



```
# 设置开机启动
systemctl enable docker
# 将指定用户添加到用户组
usermod -aG docker root
```



### docker-compose安装



```
$ sudo yum install epel-release
```

```
$ sudo yum install -y python-pip
```

```
pip install docker-compose
```







### 拉取jenkins镜像

```
docker pull jenkins/jenkins:latest
```


#### 创建一个目录

```
mkdir jenkins_co
cd jenkins_co
```



#### 编写docker-compose文件

```
vim docker-compose.yml

version: '3'
networks:
    default:
        external:
            name: my-net
services:
  jenkins:
    image: jenkins/jenkins:latest
    user: root
    container_name: jenkins
    privileged: true
    tty: true
    ports:
      - "9886:8080"
    volumes:
      - "/var/run/docker.sock:/var/run/docker.sock"
      - "./data/jenkins:/var/jenkins_home"
      - "/usr/bin/docker:/usr/bin/docker"
      - "/etc/sysconfig/docker:/etc/sysconfig/docker"
      - "/usr/bin/docker-current:/usr/bin/docker-current"
      - "/usr/local/bin/docker-compose:/usr/local/bin/docker-compose"
    restart: always
```


yml文件说明：

- privileged 给容器最高权限
- /var/run/docker.sock 、/usr/bin/docker、/etc/sysconfig/docker、/usr/bin/docker-current 挂载宿主机docker到jenkins容器，以便在武器内部可以使用
- /usr/local/bin/docker-compose 挂载docker-compose 到jenkins容器内
- ./data/jenkins 挂载数据目录


#### 启动jenkins容器

```
docker-compose up -d
```

#### 查看管理员密码


```
cat ./data/jenkins/secrets/initialAdminPassword
```

初次安装，输入查看的密码

![](/images/image-20200718115522999.png)

选择自定义安装推荐插件

![](/images/image-20200718115934006.png)


![](/images/image-20200718120008641.png)


![](/images/image-20200718120733824.png)

安装ssh插件 用于远程登录主机
![](/images/image-20200718122202538.png)



### 拉取gitlab镜像



```
docker pull gitlab/gitlab-ce
```



启动gitlab容器

```
docker run -d  -p 9884:9884 --name gitlab --restart always -v /home/gitlab/config:/etc/gitlab -v /home/gitlab/logs:/var/log/gitlab -v /home/gitlab/data:/var/opt/gitlab gitlab/gitlab-ce
```



修改基础url配置

```
vim /home/gitlab/config/gitlab.rb

external_url 
```



修改监听主机和端口

```
vim /home/gitlab/data/gitlab-rails/etc/gitlab.yml

gitlab
	host:
	port:
```



### 升级Git版本

```
vim /etc/yum.repos.d/wandisco-git.repo
```



```
[wandisco-git]
name=Wandisco GIT Repository
baseurl=http://opensource.wandisco.com/centos/7/git/$basearch/
enabled=1
gpgcheck=1
gpgkey=http://opensource.wandisco.com/RPM-GPG-KEY-WANdisco
```



```
sudo rpm --import http://opensource.wandisco.com/RPM-GPG-KEY-WANdisco
```



```
yum install git
```



```
git --version
```





### 免密ssh登录



1、产生公钥与私钥对

```
 ssh-keygen
```



2、查看本机秘钥

```
cat /root/.ssh/id_rsa
cat /root/.ssh/id_rsa.pub
```



3、将本机的公钥复制到远程机器的authorized_keys文件中

```
ssh-copy-id root@192.168.1.231
```



说明：这样做了之后，我们在jenkins里面就可以直接 ssh 插件登录多环境主机操作





### 配置jenkins部分



#### 所需插件



- [Publish Over SSH](https://plugins.jenkins.io/publish-over-ssh)
- [Git Parameter Plug-In](https://plugins.jenkins.io/git-parameter)
- [Gitlab API Plugin](https://plugins.jenkins.io/gitlab-api)
- [Gitlab Authentication plugin](https://plugins.jenkins.io/gitlab-oauth)
- [GitLab Plugin](https://plugins.jenkins.io/gitlab-plugin)
- [Docker plugin](https://plugins.jenkins.io/docker-plugin)
- [Docker API Plugin](https://plugins.jenkins.io/docker-java-api)



注：

[Git Parameter Plug-In] 插件主要实现多分支 多环境部署



#### 开始一顿配置操作



 ![image-20200719131137220](/images/image-20200719131137220.png)



![image-20200719131358422](/images/image-20200719131358422.png)



![image-20200719131742600](/images/image-20200719131742600.png)



![image-20200719125241057](/images/image-20200719125241057.png)



![image-20200719125427333](/images/image-20200719125427333.png)



![image-20200719125556247](/images/image-20200719125556247.png)



![image-20200719125655793](/images/image-20200719125655793.png)





![image-20200719125721881](/images/image-20200719125721881.png)





![image-20200719125853960](/images/image-20200719125853960.png)



![image-20200719125953821](/images/image-20200719125953821.png)



![image-20200719130215286](/images/image-20200719130215286.png)



![image-20200719130319286](/images/image-20200719130319286.png)



![image-20200719130615902](/images/image-20200719130615902.png)



![image-20200719130712348](/images/image-20200719130712348.png)



![image-20200719130739103](/images/image-20200719130739103.png)



![image-20200719132104500](/images/image-20200719132104500.png)







### 配置gitlab部分



![image-20200719132420326](/images/image-20200719132420326.png)

![image-20200719132503284](/images/image-20200719132503284.png)





完成！！！





### 最后



一般项目都是用 docker + docker-compose 容器化构建的，所以只要jenkins能够使用宿主的的这两个命令基本都OK。



有点瑕疵没解决：

​	nodejs 插件装不上，有问题，所以我是把前端包上传到部署服务器上，再使用目标服务器node环境进行项目打包部署的。。。 主要原因还是前端项目都没有容器化，都是放在主机的nginx目录下，用子目录区分。