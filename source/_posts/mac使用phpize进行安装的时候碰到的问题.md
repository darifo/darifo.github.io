---
title: mac使用phpize进行安装的时候碰到的问题
date: 2019-04-02 11:20:02
categories: 
- 经验总结
tags:
- PHP
- phpize
---


- 问题：

```
grep: /usr/include/php/main/php.h: No such file or directory
grep: /usr/include/php/Zend/zend_modules.h: No such file or directory
grep: /usr/include/php/Zend/zend_extensions.h: No such file or directory
Configuring for:
PHP Api Version:       
Zend Module Api No:    
Zend Extension Api No: 
Cannot find autoconf. Please check your autoconf installation and the
$PHP_AUTOCONF environment variable. Then, rerun this script.
```

- 解决方法：

1.重启mac电脑，同时按住command+R ,等出现进度条了再松开

2.弹出的界面是否有“实用工具”，有的话就打开实用工具里面的终端，输入：csrutil disable，然后回车

3.重新启动电脑。

4.去/usr目录下，（目录根据你自己电脑上的目录来）执行:

```
sudo ln -s /Applications/Xcode.app/Contents/Developer/Platforms/MacOSX.platform/Developer/SDKs/MacOSX10.14.sdk/usr/include/  /usr
```

5.你再去尝试执行phpize就OK了


![](/images/11251887.png)