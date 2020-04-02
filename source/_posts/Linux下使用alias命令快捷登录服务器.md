---
title: Linux下使用alias命令快捷登录服务器
date: 2019-07-18 11:00:44
categories: 
- 技能技巧
tags:
- Linux
- alias 
---


编辑 **~/.bash_profile** 配置文件，末尾加上：

```shell
alias myserv='ssh darifo@*.*.*.*'
alias me='echo darifo'
```

配置ssh key

```
~/.ssh
id_rsa.pub
```

生成ssh-key

```
ssh-keygen
```
