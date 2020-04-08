---
title: 在Docker上部署家庭影院系统jellyfin
author: Darifo
Github: 'https://github.com/darifo'
Pages: 'https://darifo.github.io/'
date: 2020-04-07 22:17:36
categories:
- 软件安装
tags:
- Docker
- Jellyfin
---

### jellyfin

```shell
docker pull jellyfin/jellyfin
docker run -d --name jellyfin_server -p 0.0.0.0:8096:8096 -v /e/docker/jellyfin/config:/config -v /e/docker/jellyfin/media:/media jellyfin/jellyfin
```

![](/images/20200407223553.png)