---
title: 最近使用的一些SQL总结
date: 2019-01-12 12:03:31
categories: 
- 经验总结
tags:
- MYSQL
---


## 背景

最近在做一个数据导出的小需求，需要从多个表查询关联的数据并输出，同时涉及一部分业务相关的简单逻辑处理；


#### 解决问题思路：

既然是用sql查询去筛选数据并导出，而且又涉及很多张表关联以及一部分字段的计算，我的初步方案是：

> 拆分逻辑，通过层层查询的嵌套，最外层实现一些逻辑和计算




#### 具体实现：


首先，我用了最基础的查询结构：


```sql

select * from table_A where ...;

```

这是最里层，需要查询一些必须的基础数据，就好比一个程序的入口，以此为突破点，查询到的数据作为下一步的条件，继续查询；


接下来我是这样写的：


```sql

select a.* , table_B.* from (
    select * from table_A where ...
) a , table_B where a.xid =  table_B.xid ... ;

```

这里就是以第一步的查询结果作为数据源，进行二次查询，同时加上条件筛选；

接下来的操作就根据业务复杂度进行，完全可以像这样：


```sql
select b.* , table_C.* from (
    select * from (
        select * from table_A where ...
    ) a , table_B where a.xid =  table_B.xid ... 
) b , table_C where b.aid = table_C.aid ... ;

```



进行多次的查询，直到所需要的数据字段都查询出来之后，你可以在最外层加入一些基础计算：


```sql

select 
    c.aaaa                                              as AAAA,
    ROUND((c.bbbb/c.cccc ),2)                           as BBBB,  # 结果保留两位小数
    ifnull(c.cccc,0)                                    as CCCC,  # 如果结果为null，返回0
    if(table_D.dddd>0,if(table_D.dddd>100,100,0),0)     as DDDD,  # 条件语句
    case table_D.eeee
        when 0 then 'a'
        when 1 then 'b'
        when 2 then 'c'
        else 'd'
    end                                                 as    EEEE  # 类似于 switch case 用法
    ....
from (
    select b.* , table_C.* from (
        select * from (
            select * from table_A where ...
        ) a , table_B where a.xid =  table_B.xid ... 
    ) b , table_C where b.aid = table_C.aid ... 
) c , table_D ...;


```

当然，这些都是很简单的操作，很多时候都需要对数据进行 分组、求和、统计、排序等操作；那就需要根据特定的需求进行语句的改造和优化；

例如：

```sql

select a.* , count(table_B.aaa) as AAAA ,sum(a.cccc) as CCCC from (
    select * from table_A where ...
) a , table_B where a.xid =  table_B.xid  group by table_B.aaa ... ;

```

就是在某一层的查询，进行了分组统计求和操作；


再复杂一点还可以加上 连接（左连接、右连接等）、自查询、子查询等操作；


例如：


```sql
select c.*, table_D.*, aa.*
from (
    select b.* , table_C.* from (
        select * from (
            select * from table_A where ...
        ) a , table_B where a.xid =  table_B.xid ... 
    ) b , table_C where b.aid = table_C.aid ... 
) c , table_D ...

left join 
        (select * from table_A where ...) as aa 
    on aa.aid = c.aid
    
where ...
;

```

哈哈，当你发现运行上面这个结构的语句时，会报一个错误 ： “找不到 c.aid 字段！”，因为这里比较特殊，连接查询两边都是 查询 而不是真实表，所以都需要用括号把 left join 两端的完整查询给扩起来，如下：

```sql
select c.*, table_D.*, aa.*
from (
    (
        select b.* , table_C.* from (
            select * from (
                select * from table_A where ...
            ) a , table_B where a.xid =  table_B.xid ... 
        ) b , table_C where b.aid = table_C.aid ... 
    ) c , table_D ...
)

left join 
        (select * from table_A where ...) as aa 
    on aa.aid = c.aid
    
where ...
;

```

只需要在from后面的查询两端加上括号包起来就OK了。


其实，在以上多次查询嵌套的使用过程中，还有一个需要注意的细节就是：每一个内层查询的字段有可能和外层查询的字段有重复的情况，如果你在当前层查询的外层查询中要使用存在重复的字段，需要在内层查询的 select 输出字段列表的时候，就要指定该字段的来源表或查询，否则，会提示字段不存在错误，所以 不要一味使用 * 输出字段列表；

例如：


```sql
select b.* , table_C.* from (
    select a.*, table_B.* from (
        select * from table_A where ...
    ) a , table_B where a.xid =  table_B.xid ... 
) b , table_C where b.oid = table_C.oid ... ;

```

如果内层查询两张表或查询都有 oid 字段，上述语句会报错，“字段 b.oid 不存在”，因为内层查询输出字段 没有指定这个 oid 是来源于哪一个表或查询；

正确的应该是这样写：

```sql
select b.* , table_C.* from (
    select a.*, table_B.oid from (
        select * from table_A where ...
    ) a , table_B where a.xid =  table_B.xid ... 
) b , table_C where b.oid = table_C.oid ... ;

```

以上，b 作为上一层查询的所有输出结果，所以 b 是包含 a 集合和 table_B 集合的并集，并且内层指定了 oid 来源于 table_B 表，所以语句正确；



----


#### 未完待续......

