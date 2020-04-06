---
title: MySQL基础知识点总结
author: Darifo
Github: 'https://github.com/darifo'
Pages: 'https://darifo.github.io/'
date: 2020-04-04 23:19:00
categories:
- 数据库
tags:
- MySQL
---


### 存储引擎：MyISAM 和 InnoDB

1、MyISAM 

- MySQL 5.5版本前默认存储引擎
- 全文索引，不支持 事务 和 行级锁，崩溃无法安全恢复
- 适用于 读密集 场景
- 只支持表级锁
- 查询具有原子性，速度更快

2、InnoDB

- MySQL 5.5之后 默认存储引擎
- 支持 行级锁 和 表级锁 ，默认行级锁
- 支持事务、安全恢复、外键等高级功能
- 支持 MVCC，高效应对高并发事务


### 索引

MySQL 索引 主要数据结构 分为 BTree 和 哈希，底层结构就是对应 BTree 和 哈希表。

#### B 树



#### 非聚集索引

MyISAM 非聚簇索引



#### 聚集索引

InnoDB 聚簇索引



#### 联合索引



### 最左前缀原则




