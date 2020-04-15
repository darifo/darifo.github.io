---
title: 【Java设计模式】工厂模式
author: Darifo
Github: 'https://github.com/darifo'
Pages: 'https://darifo.github.io/'
date: 2020-04-13 23:29:44
categories:
- 设计模式
tags:
- JAVA
- 工厂模式
---


### 定义

在基类中定义创建对象的一个接口，让子类决定实例化哪个类。工厂方法让一个类的实例化延迟到子类中进行。


根据抽象程度，分为三种模式：

- 简单工厂模式（ Simple Factory ），又称静态工厂方法模式
- 工厂方法模式（ Factory Method ），又称多态性工厂模式或虚拟构造子模式
- 抽象工厂模式（ Abstract Factory ），又称工具箱（Kit 或Toolkit）模式

### 意义

- 解耦 ：把对象的创建和使用的过程分开
- 降低代码重复: 如果创建某个对象的过程都很复杂，需要一定的代码量，而且很多地方都要用到，那么就会有很多的重复代码。
- 降低维护成本 ：由于创建过程都由工厂统一管理，所以发生业务逻辑变化，不需要找到所有需要创建对象B的地方去逐个修正，只需要在工厂里修改即可，降低维护成本。


### 简单工厂

又称静态工厂方法模式，严格的说，简单工厂模式并不是23种常用的设计模式之一，它只算工厂模式的一个特殊实现。


角色：

1、抽象产品（一个）

2、具体产品（多个）

3、具体工厂（一个）


思路：

- 首先我们抽象出来一个产品（物品）【注：名词 使用 抽象类，形容词 使用 接口】

这里我抽象出 书类 ，所以 写的是一个 抽象类 

定义了一个 要实现的抽象方法 “读书”，不管具体是什么书，都可以去阅读学习

```java
package com.company.darifo.design.pattern.factories.simple;

public abstract class Book {
    abstract void read();
}
```

- 然后，我再分别 定义 两个具体类 java书 和 python书 去继承 抽象类 书，并且实现抽象方法 

```java
package com.company.darifo.design.pattern.factories.simple;

public class JavaBook extends Book {
    @Override
    void read() {
        System.out.println("java book learning...");
    }
}
```
```java
package com.company.darifo.design.pattern.factories.simple;

public class PythonBook extends Book {
    @Override
    void read() {
        System.out.println("python book learning...");
    }
}
```

- 接着，我编写一个工厂类，去实现 不同书籍的创建工作

```java
package com.company.darifo.design.pattern.factories.simple;

public class BookFactory {
    public Book createBook(String name){
        if (name.equals("Java"))
            return new JavaBook();
        if (name.equals("Python"))
            return new PythonBook();
        return new JavaBook();
    }
}
```

- 最后，我们去调用工厂，生产对应的产品（物品）对象

```java
package com.company.darifo.design.pattern.factories.simple;

public class Main {
    public static void main(String[] args){
        BookFactory f = new BookFactory();

        Book javaBook = f.createBook("Java");
        Book pythonBook = f.createBook("Python");

        javaBook.read();
        pythonBook.read();
    }
}
```
结果输出：
```
java book learning...
python book learning...
```

以上，就完成了一个简单工厂。

有个问题，如果有新增的书籍，那么我就要不断修改工厂类里面的 条件判断 ，以适应新的产品加入，显然这样是很繁琐的，违背 “开闭原则”。

#### 开闭原则

    开闭原则(OCP)是面向对象设计中“可复用设计”的基石,

    是面向对象设计中最重要的原则之一,

    其它很多的设计原则都是实现开闭原则的一种手段。

        开闭原则中“开”, 是指对于组件功能的扩展是开放的, 是允许对其进行功能扩展的;

        开闭原则中“闭”, 是指对于原有代码的修改是封闭的, 即不应该修改原有的代码。


### 工厂方法

工厂方法模式去掉了简单工厂模式中工厂方法的静态属性，使得它可以被子类继承。这样在简单工厂模式里集中在工厂方法上的压力可以由工厂方法模式里不同的工厂子类来分担。

也就是拆分得更细致了，

角色：

1、抽象产品（一个）

2、具体产品（多个）

3、抽象工厂（一个）

4、具体工厂（多个）

这样，工厂就被提炼出来一层，规定要做的一些事情


实现：

- 首先，定义一个抽象产品 代码 ，抽象方法 写代码

```java
package com.company.darifo.design.pattern.factories.func;

public abstract class Code {
    abstract void write();
}
```

- 然后 分别定义两个 具体产品 java代码 和 python代码 继承 代码

```java
package com.company.darifo.design.pattern.factories.func;

public class JavaCode extends Code {
    @Override
    void write() {
        System.out.println("writing java code...");
    }
}
```

```java
package com.company.darifo.design.pattern.factories.func;

public class PythonCode extends Code {
    @Override
    void write() {
        System.out.println("writing python code...");
    }
}
```

- 然后，定义一个抽象工厂（接口类型），因为  写什么 不是名词，并且抽象出 创建代码器的抽象方法，实现接口就必须实现创建对象的这个方法

```java
package com.company.darifo.design.pattern.factories.func;

public interface WriteFactory {
    Code createCode();
}
```

- 接着，在定义两个具体工厂类 ， 分别是 java工厂 和 python工厂，这两个工厂都可以创建一个用于写代码的对象， 并且都实现抽象工厂接口 写工厂

```java
package com.company.darifo.design.pattern.factories.func;

public class JavaFactory implements WriteFactory {
    @Override
    public Code createCode() {
        return new JavaCode();
    }
}
```
```java
package com.company.darifo.design.pattern.factories.func;

public class PythonFactory implements WriteFactory {
    @Override
    public Code createCode() {
        return new PythonCode();
    }
}
```

- 最后，使用工厂演示

```java
package com.company.darifo.design.pattern.factories.func;

public class Main {
    public static void main(String[] args) {
        JavaFactory javaFactory = new JavaFactory();
        PythonFactory pythonFactory = new PythonFactory();

        Code javaCode = javaFactory.createCode();
        Code pythonCode = pythonFactory.createCode();

        javaCode.write();
        pythonCode.write();
    }
}
```

输出结果为：

```
writing java code...
writing python code...
```

工厂方法模式仿佛已经很完美的对对象的创建进行了包装，

使得客户程序中仅仅处理抽象产品角色提供的接口，

但使得对象的数量成倍增长。

当产品种类非常多时，会出现大量的与之对应的工厂对象，

即要写很多类（类爆炸），这不是我们所希望的。



### 抽象工厂


产品设计背景： 

程序员A：看书（JAVA书）、写代码（JAVA代码）
程序员B：看书（Python书）、写代码（Python代码）
程序员C：看书（JAVA书）、写代码（Python代码）
...

抽象层：

程序员、书、代码

抽象程序员工厂（创建书、创建代码）

具体工厂（A、B、C）

抽象产品（书、代码）

具体产品（JAVA书、Python书、Java代码、Python代码）

...


实现：

- 首先，抽象出两个产品族，书籍、代码

```java
package com.company.darifo.design.pattern.factories.abs;

public abstract class AbstractBook {
    abstract void learn();
}
```

```java
package com.company.darifo.design.pattern.factories.abs;

public abstract class AbstractCode {
    abstract void write();
}
```

- 然后，增加具体书籍产品族产品，java书、python书、php书

```java
package com.company.darifo.design.pattern.factories.abs;

public class JavaBook extends AbstractBook {
    @Override
    void learn() {
        System.out.println("java book learning...");
    }
}
```

```java
package com.company.darifo.design.pattern.factories.abs;

public class PythonBook extends AbstractBook {
    @Override
    void learn() {
        System.out.println("python book learning...");
    }
}
```

```java
package com.company.darifo.design.pattern.factories.abs;

public class PhpBook extends AbstractBook {
    @Override
    void learn() {
        System.out.println("php book learning...");
    }
}
```

- 接着，增加具体代码产品族产品，java代码、python代码、php代码

```java
package com.company.darifo.design.pattern.factories.abs;

public class JavaCode extends AbstractCode {
    @Override
    public void write(){
        System.out.println("write java code");
    }
}
```

```java
package com.company.darifo.design.pattern.factories.abs;

public class PythonCode extends AbstractCode {

    @Override
    public void write(){
        System.out.println("write python code");
    }
}
```

```java
package com.company.darifo.design.pattern.factories.abs;

public class PhpCode extends AbstractCode {
    @Override
    void write() {
        System.out.println("write php code");
    }
}

```

- 然后，创建抽象工厂，程序员

```java
package com.company.darifo.design.pattern.factories.abs;

public abstract class AbstractCoderFactory {
    abstract AbstractBook createBook();
    abstract AbstractCode createCode();
}
```

- 继续创建具体工厂，java程序员工厂、python程序员工厂、php程序员工厂

```java
package com.company.darifo.design.pattern.factories.abs;

public class JavaCoderFactory extends AbstractCoderFactory {
    @Override
    AbstractBook createBook() {
        return new JavaBook();
    }

    @Override
    AbstractCode createCode() {
        return new PythonCode();
    }
}
```

```java
package com.company.darifo.design.pattern.factories.abs;

public class PythonCoderFactory extends AbstractCoderFactory {
    @Override
    AbstractBook createBook() {
        return new PythonBook();
    }

    @Override
    AbstractCode createCode() {
        return new JavaCode();
    }
}
```

```java
package com.company.darifo.design.pattern.factories.abs;

public class PhpCoderFactory extends AbstractCoderFactory {
    @Override
    AbstractBook createBook() {
        return new PhpBook();
    }

    @Override
    AbstractCode createCode() {
        return new PhpCode();
    }
}
```

- 最后测试一下工厂

```java
package com.company.darifo.design.pattern.factories.abs;

public class Main {

    public static void main(String[] args){

        AbstractCoderFactory f = new JavaCoderFactory();
        AbstractBook book = f.createBook();
        AbstractCode code = f.createCode();
        book.learn();
        code.write();

        AbstractCoderFactory f1 = new PythonCoderFactory();
        AbstractBook book1 = f1.createBook();
        AbstractCode code1 = f1.createCode();
        book1.learn();
        code1.write();

        AbstractCoderFactory f2 = new PhpCoderFactory();
        AbstractBook book2 = f2.createBook();
        AbstractCode code2 = f2.createCode();
        book2.learn();
        code2.write();
    }
}
```

如下图：

![](/images/20200415011448.png)


1、将各类产品抽象出来分为很多族

2、实现具体产品类

3、抽象出生产工厂

4、实现各类具体工厂

5、具体产品对象在具体工厂里面创建