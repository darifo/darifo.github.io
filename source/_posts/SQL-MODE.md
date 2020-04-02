---
title: SQL_MODE
date: 2019-01-14 12:00:27
categories: 
- 经验总结
tags:
- MYSQL
---

## Mysql TIMESTAMP 字段「Invalid default value」问题

这是我在Mysql数据库中，新建一张表时出现的问题，代码如下

-- 创建表

```sql
CREATE TABLE seckill(
  `seckill_id` bigint NOT NULL AUTO_INCREMENT COMMENT '商品库存id',
  `name` varchar(120) NOT NULL COMMENT '商品名称',
  `number` int NOT NULL COMMENT '库存数量',

  -- 此行不会报错，因为默认值使用CURRENT_TIMESTAMP
  `start_time` TIMESTAMP NOT NULL COMMENT '秒杀开启时间',

  -- 此行会报错，因为默认值使用“0000-00-00 00:00:00”，
  -- Mysql 5.7版本后的SQL_MODE默认为strict（NO_ZERO_DATE），
  -- 所以第二个出现报错
  -- 解决方案：SET SQL_MODE = 'ALLOW_INVALID_DATES';

  `end_time` TIMESTAMP NOT NULL COMMENT '秒杀结束时间',
  `create_time` TIMESTAMP NOT NULL DEFAULT CURRENT_TIMESTAMP COMMENT '创建时间',
  PRIMARY KEY (seckill_id),
  key idx_start_time(start_time),
  key idx_end_time(end_time),
  key idx_create_time(create_time)
)ENGINE=InnoDB AUTO_INCREMENT=1000 DEFAULT CHARSET=UTF8 COMMENT='秒杀库存表';
```

我的解决方案是：

```
SET SQL_MODE = 'ALLOW_INVALID_DATES';
```


