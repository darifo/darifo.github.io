---
title: 【Java设计模式】单例模式
author: Darifo
Github: 'https://github.com/darifo'
Pages: 'https://darifo.github.io/'
date: 2020-04-12 23:30:01
categories:
- 设计模式
tags:
- 单例模式
- Singleton
---


这种类型的设计模式属于创建型模式，它提供了一种创建对象的最佳方式。

这种模式涉及到一个单一的类，该类负责创建自己的对象，同时确保只有单个对象被创建。这个类提供了一种访问其唯一的对象的方式，可以直接访问，不需要实例化该类的对象。

注：

1、单例类只能有一个实例。

2、单例类必须自己创建自己的唯一实例。

3、单例类必须给所有其他对象提供这一实例。


### 饿汉式，线程安全

```java
package com.company.darifo.design.pattern.singleton;

public class Singleton1 {

    // 定义 实例常量 final 必须初始化 new 一个对象
    // 类加载时 实例化 【饿汉式】
    private static final Singleton1 INSTANCE = new Singleton1();

    // 私有化构造方法
    private Singleton1(){};

    // 开放静态类方法 返回对象实例
    public static Singleton1 getInstance(){
        return INSTANCE;
    }

    public static void main(String[] args){

        Singleton1 obj1 = Singleton1.getInstance();
        Singleton1 obj2 = Singleton1.getInstance();

        // 同一类的两个对象 hashCode 相同 ，属于同一个对象
        System.out.println(obj1.hashCode());
        System.out.println(obj2.hashCode());
    }
}
```

### 懒汉式-01， 线程不安全

#### volatile
    一旦一个共享变量（类的成员变量、类的静态成员变量）被 volatile 修饰之后，
    
    那么就具备了两层语义：

    　　1.保证了不同线程对这个变量进行操作时的可见性，
        即一个线程修改了某个变量的值，这新值对其他线程来说是立即可见的。

    　　2.禁止进行指令重排序。

```java
package com.company.darifo.design.pattern.singleton;

public class Singleton2 {

    // 这里我们 使用 volatile 修饰 ，保证不进行指令重排，以免破坏单例对象
    private static volatile Singleton2 INSTANCE;

    private Singleton2() {
    }

    // 懒汉式
    public static Singleton2 getInstance() {
        if (INSTANCE == null) {

            // 为了更明显突出 多线程下的 效果，这里设置延时
            try {
                Thread.sleep(1);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }

            INSTANCE = new Singleton2();
        }
        return INSTANCE;
    }

    public static void main(String[] args) {

        // 启动 100 个线程同时 去获取 单例对象
        for (int i = 0; i < 100; i++) {
            new Thread(() -> {
                System.out.println(Singleton2.getInstance().hashCode());
            }).start();
        }

    }
}
```


### 懒汉式-02 （加方法同步锁），线程安全

```java
package com.company.darifo.design.pattern.singleton;

public class Singleton3 {
    private static volatile Singleton3 INSTANCE;

    private Singleton3() {
    }

    public static synchronized Singleton3 getInstance() {
        if (INSTANCE == null) {

            try {
                Thread.sleep(1);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }

            INSTANCE = new Singleton3();
        }
        return INSTANCE;
    }

    public static void main(String[] args) {

        for (int i = 0; i < 100; i++) {
            new Thread(() -> {
                System.out.println(Singleton3.getInstance().hashCode());
            }).start();
        }
    }
}
```


### 懒汉式-03（加代码块同步锁），线程不安全

```java

package com.company.darifo.design.pattern.singleton;

public class Singleton4 {

    private static volatile Singleton4 INSTANCE;

    private Singleton4() {
    }
    
    public static Singleton4 getInstance() {
        if (INSTANCE == null) {
            // synchronized
            // 当括号里是this时，锁的是当前调用方法的对象
            // 当括号里是一个Class对象时，锁的是这个类，也就是同一个类的实例，任意时刻只会有一个线程能获得资源
            synchronized (Singleton4.class) {
                try {
                    Thread.sleep(1);
                    INSTANCE = new Singleton4();
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            }
        }
        return INSTANCE;
    }

    public static void main(String[] args) {
        for (int i = 0; i < 100; i++) {
            new Thread(() -> {
                System.out.println(Singleton4.getInstance().hashCode());
            }).start();
        }
    }
}

/*
 * 对象锁: synchronized(object) 锁住的是对象,每个对象自己拥有一个锁
 * 类锁: synchronized(Class) 锁住的是类，也就是同一个类的实例，任意时刻只会有一个线程能获得资源
 *
 * */
```

### 懒汉式-04（加代码块同步锁 + 实例双重判断）双检锁/双重校验锁（DCL，即 double-checked locking），线程安全

```java
package com.company.darifo.design.pattern.singleton;

public class Singleton5 {

    private static volatile Singleton5 INSTANCE;

    private Singleton5() {
    }

    public static Singleton5 getInstance() {

        if (INSTANCE == null) {

            synchronized (Singleton5.class) {

                if (INSTANCE == null) {
                    try {
                        Thread.sleep(1);
                    } catch (InterruptedException e) {
                        e.printStackTrace();
                    }
                    INSTANCE = new Singleton5();
                }
            }
        }
        return INSTANCE;
    }

    public static void main(String[] args) {
        for (int i = 0; i < 100; i++) {
            new Thread(() -> {
                System.out.println(Singleton5.getInstance().hashCode());
            }).start();
        }
    }
}
```


### 静态内部类/登记式，线程安全

```java

package com.company.darifo.design.pattern.singleton;

public class Singleton6 {

    private Singleton6(){}

    private static class MySingleton6{
        private final static Singleton6 INSTANCE = new Singleton6();
    }

    public static Singleton6 getInstance(){
        return MySingleton6.INSTANCE;
    }

    public static void main(String[] args){
        for (int i = 0 ; i < 100 ;i++){
            new Thread(()->{
                System.out.println(Singleton6.getInstance().hashCode());
            }).start();
        }
    }
}
```


### 枚举单例 【完美】，线程安全，反射安全

- 代码最完美单例实现
- 简洁，自动支持序列化机制，绝对防止多次实例化。

```java
package com.company.darifo.design.pattern.singleton;

/**
 *
 * 枚举的单例模式【最完美】  没有构造方法，所以不能反序列化
 *
 * 在<<effective java>>书中提到，建议使用枚举类做单例模式，
 * 能够很好的保护代码隐私，
 * 即使使用反射也不能创建对象。
 * 通过以下的两种代码对比，能够很直观的展现枚举实现的单例模式：
 *
 * */
public enum Singleton7 {

    INSTANCE;

    public void func(){
        System.out.println("枚举的单例模式");
    }

    public static void main(String[] args){
        for (int i = 0; i < 100; i++){
            new Thread(()->{
                System.out.println(Singleton7.INSTANCE.hashCode());
                Singleton7.INSTANCE.func();
            }).start();
        }
    }
}
```

