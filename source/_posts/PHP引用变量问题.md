---
title: PHP引用变量问题
date: 2019-01-02 11:38:58
categories: 
- 经验总结
tags:
- PHP
- 引用变量
---





附上代码：


```php
$orders = [
    ['id'=>10001,'channel_type'=>3],
    ['id'=>10002,'channel_type'=>2],
];

foreach ($orders as &$order){
    $order['order_price'] = 0 ;
}

var_dump($orders);

foreach($orders as $order){
    var_dump($order);
}
```

说明：

有一个订单数组，我想要在每一个元素上加上一个 order_price 属性，所以第一个遍历使用了 引用符号 & ，在第一个var_dump时会输出我添加成功后的订单数组，包含了订单价格属性，接着我又在后面的业务逻辑中要再次去遍历这个订单数组，我使用了同样的一个别名 “$order”，然后在循环中输出订单元素，发现一个很奇怪的事情，两次输出的订单竟然是同一个：

['id'=>10002,'channel_type'=>2,'order_price'=>0]


经过请教，发现是因为这个相同的变量名 “$order” 导致的，只需要改成其他名称就可以正常遍历数组内容了；

```php
foreach($orders as $ord){
    var_dump($ord);
}
```


具体原因分析：

解释:

1.foreach不是块级域，数组引用结束，$k,$a没有释放内存

2.当第一次引用循环结束的时候，$a指向数组最后一个元素4

3.当第二次开始循环 (此时$a已经是指向最后一个元素，是引用)


循环4次  数组的变化分别是:

1->    [1,2,3,4]

2->    [1,2,3,4]

3->    [1,2,3,3]    

第三次value指向最后一个元素3   

但是value是全局引用在上次循环指向了最后一个元素  所以value=3的时候 也会把最后一个元素的值改为3

4->    [1,2,3,3]



注意事项：

1.foreach 实现原理有个内部指针的概念    

2.&的foreach循环结束   指针会指向最后一个元素



如何避免：

在用&foreach循环后  unset  ($a)



