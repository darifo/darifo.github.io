---
title: centos7上的gitlab安装记录
date: 2019-03-02 11:30:33
categories: 
- 经验总结
tags:
- gitlab
---


安装记录：

1、wget  https://mirrors.tuna.tsinghua.edu.cn/gitlab-ce/yum/el7/gitlab-ce-11.8.0-ce.0.el7.x86_64.rpm  

2、yum install policycoreutils-python 

3、rpm -i gitlab-ce-11.8.0-ce.0.el7.x86_64.rpm

4、vim /etc/gitlab/gitlab.rb   ：external_url -> 服务器http://IP:端口

5、gitlab-ctl reconfigure

6、gitlab-ctl restart

￼

更改端口

```
vim /etc/gitlab/gitlab.rb
```

输入内容：

```
external_url 'http://XXXX:8088/'

nginx['listen_port'] = 8088

nginx['listen_https'] = false
```

改nigx端口

```
vim /var/opt/gitlab/nginx/conf/gitlab-http.conf
```

查找如下内容并修改：

```
server {
listen *:8088;
server_name XXXX
if ($http_host = "") {
   set $http_host_with_default "XXXX:8088/";
}
}
```

修改完成后，执行

```
gitlab-ctl reconfigure
gitlab-ctl restart
```

访问：

http://XXXX:8088/


