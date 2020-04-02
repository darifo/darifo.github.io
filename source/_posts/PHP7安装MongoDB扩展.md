---
title: PHP7安装MongoDB扩展(Linux)
categories: 
- 环境安装
tags:
- PHP
- MongoDB
date: 2019-7-23 10:14:03
---


## 方法1:

官网下载压缩包：https://pecl.php.net/package/mongodb

step1:

```
tar -zxvf 包名
```

- step2:

```
phpize

./configure 

make 

sudo make install
```

php.ini加上

```
extension=mongodb.so
```

## 方法2:

安装pecl：

```
curl -O https://pear.php.net/go-pear.phar

sudo php -d detect_unicode=0 go-pear.phar
```

一直回车

```
pecl version
```

安装mongo：

```
pecl install mongodb-1.5.3.tgz
```

这里会提示写权限，加上 sudo 即可

使用pecl安装会自动执行phpize、configure、make、make install 推荐使用；


编译问题(Security/Security.h can't be found in macOS)：

>fixed, the real problem is Security/Security.h can't be found in macOS 10.14.3, and there is another similar problem to CoreFoundation/CFBase.h it can be fixed by following steps:

解决：
```
cd mongodb-1.5.3/include

ln -s /Library/Developer/CommandLineTools/SDKs/MacOSX.sdk/System/Library/
Frameworks/Security.framework/Versions/A/Headers/ Security

ln -s /Library/Developer/CommandLineTools/SDKs/MacOSX.sdk/System/Library/Frameworks/CoreFoundation.framework/Versions/A/Headers/ CoreFoundation

brew install pcre

make
```