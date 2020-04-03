---
title: 详细理解String、StringBuffer、StringBuilder区别用途
author: Darifo
Github: 'https://github.com/darifo'
Pages: 'https://darifo.github.io/'
date: 2019-12-20 18:45:01
categories:
- 编程语言
tags:
- JAVA
- String
- StringBuffer
- StringBuilder
---


### String

定义：

```java
private final char[] value;
```

java 9 后改用 byte 数组存储，

```java
private final byte[] value;
```

1、定义使用了final关键词，所以，**String对象是不可变的**

2、可以把 String 对象理解为 常量 ，不可变，即 **线程安全**

3、每次对 String 类型进行改变的时候，都会重新生成一个 String 对象，然后指向新的 String 对象。


### StringBuffer

继承自 抽象类 AbstractStringBuilder 

```java
abstract class AbstractStringBuilder implements Appendable, CharSequence {
 /**
 * The value is used for character storage.
 */
 char[] value;
 /**
 * The count is the number of characters used.
 */
 int count;
 AbstractStringBuilder(int capacity) {
 value = new char[capacity];
 }
 ```

定义：

```java
private char[] value;
```

1、没有使用 final 关键词修饰，所以 **可变**

2、StringBuffer 对调用方法加了**同步锁**, 所以多线程下，是**线程安全**的

3、StringBuffer每次操作都是对 StringBuffer 对象本身进行的，而没有生成新的对象和引用


### StringBuilder

同样继承自 AbstractStringBuilder

1、**可变**

2、StringBuilder并没有对方法加同步锁，所以是**非线程安全**的

3、同等操作下 StringBuilder 比StringBuffer 性能略好，但是多线程下有风险



#### 总结

1、操作少量数据适用 String

2、单线程 大量数据操作 StringBuilder

3、多线程 大量数据操作 StringBuffer

