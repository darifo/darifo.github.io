---
title: Vue + axios 异步请求遇到的一些问题和解决
date: 2019-01-17 11:45:59
categories: 
- 经验总结
tags:
- Vue
- axios
- 跨域
- Option请求
---


技术栈
> Vue + Vue-router + axios + element-UI  &&  ThinkPHP5


# 1、使用axios异步请求跨域问题解决

解决跨域问题常用的解决方案有两个：

> JSONP：利用script标签可跨域的特点，在跨域脚本中可以直接回调当前脚本的函数。


> CORS：服务器设置HTTP响应头中Access-Control-Allow-Origin值，解除跨域限制。


- 后端解除限制

使用的TP5框架，在入口文件 index.php 添加如下代码（比较暴力的配置，详细的可以看一下文档末尾的说明）：

```
header('Access-Control-Allow-Origin:*');  // * 允许所有主机连接
header('Access-Control-Allow-Methods:*');  // * 允许所有类型请求方法
header('Access-Control-Allow-Headers:*');  // * 允许所有头
```

- 前端方案

代理 

> 正向代理、反向代理


不是很了解，有待学习····



# 2、发送 OPTION 预检请求导致的接口错误解决

背景：


其实这个问题在之前项目开发中遇到过多次了，对前端（vue、axios）也不是很熟练，一直没总结，导致最近又遇到了，还是犯这种错，所以在这里简单总结了一下，希望以后遇到能够很快定位到问题所在；



我在使用axios发送异步请求的时候，会发起两次“重复”请求，实际上是两次不同的请求，

第一次是以 OPTION 方式 发起的 预检请求 ，也就是提前告诉服务器，我要以什么方式去请求你啦，做好准备哦！这个信息是通过 HTTP 请求头的 “Access-Control-Request-Method” 传递的，==这个预请求不会带参数到后端，所以我们后端接口往往获取不到此类请求的参数，这也就是为什么接口一般会 500 的原因啦==；


![image](/images/20200402114820.png)


第二次请求是真实请求，也就是我们想要发送的 POST 类型请求，真实请求会带上请求参数，向后端获取对应的数据，这时后端接口才能正常获取参数，执行逻辑代码，返回数据，完成整个流程；
![image](/images/20200402114858.png)




所以，为了不出现 500 的致命错误，代码逻辑还需要相当的严谨才好啊～～～


比如，对于接口参数，接收或是转成数组、对象 等 操作的时候 ， 最好不要直接用下标或者属性去获取，还是先验证一番！至少知道错在哪，不会导致错误被直接抛出，也不利于程序安全，同样，try...catch 代码块也体现出相当重要的地位。

![image](/images/20200402114919.png)




- 接口过滤请求，只接收 指定类型的请求处理逻辑；

tp5 继承 Controller 类可调用request->isPost()

```
if ($this->request->isPost()){
    // 处理逻辑，获取参数
}else{
    // 返回
}
```


- 设置头“Access-Control-Allow-Methods”，不允许 OPTION 请求；


```
header('Access-Control-Allow-Methods:POST,GET');
```



---


# 相关文档说明：

## HTTP 响应首部字段

 > ### Access-Control-Allow-Credentials

响应头表示是否可以将对请求的响应暴露给页面。返回true则可以，其他值均不可以。
Credentials可以是 cookies, authorization headers 或 TLS client certificates。
当作为对预检请求的响应的一部分时，这能表示是否真正的请求可以使用credentials。注意简单的GET 请求没有预检，所以若一个对资源的请求带了credentials，如果这个返回这个资源，响应就会被浏览器忽视，不会返回到web内容。

Access-Control-Allow-Credentials 头 工作中与XMLHttpRequest.withCredentials 或Fetch API中的Request() 构造器中的credentials 选项结合使用。Credentials必须在前后端都被配置（即the Access-Control-Allow-Credentials header 和 XHR 或Fetch request中都要配置）才能使带credentials的CORS请求成功。

语法：
```
Access-Control-Allow-Credentials: true
```

> ## Access-Control-Allow-Origin

指定了允许访问该资源的域

语法：

```
Access-Control-Allow-Origin: <origin> | *
```
origin 参数的值指定了允许访问该资源的外域 URI。对于不需要携带身份凭证的请求，服务器可以指定该字段的值为通配符，表示允许来自所有域的请求。

如果服务端指定了具体的域名而非“*”，那么响应首部中的 Vary 字段的值必须包含 Origin。这将告诉客户端：服务器对不同的源站返回不同的内容。



> ## Access-Control-Allow-Methods

指明了实际请求所允许使用的 HTTP 方法。

语法：

```
Access-Control-Allow-Methods: <method>[, <method>]*
```



> ## Access-Control-Allow-Headers

指明了实际请求中允许携带的首部字段。

语法：

```
Access-Control-Allow-Headers: <field-name>[, <field-name>]*
```

> ## Access-Control-Expose-Headers

在跨域访问时，XMLHttpRequest对象的getResponseHeader()方法只能拿到一些最基本的响应头，Cache-Control、Content-Language、Content-Type、Expires、Last-Modified、Pragma，如果要访问其他头，则需要服务器设置本响应头。

Access-Control-Expose-Headers 头让服务器把允许浏览器访问的头放入白名单，例如


```
Access-Control-Expose-Headers: X-My-Custom-Header, X-Another-Custom-Header
```

这样浏览器就能够通过getResponseHeader访问X-My-Custom-Header和 X-Another-Custom-Header 响应头了。

> ## Access-Control-Max-Age

指定了preflight请求的结果能够被缓存多久。

```
Access-Control-Max-Age: <delta-seconds>
```


delta-seconds 参数表示preflight请求的结果在多少秒内有效。


## HTTP 请求首部字段


> ## Origin

表明预检请求或实际请求的源站。

```
Origin: <origin>
```

origin 参数的值为源站 URI。它不包含任何路径信息，只是服务器名称。

> ## Access-Control-Request-Method

用于预检请求。其作用是，将实际请求所使用的 HTTP 方法告诉服务器。


```
Access-Control-Request-Method: <method>
```


> ## Access-Control-Request-Headers

用于预检请求。其作用是，将实际请求所携带的首部字段告诉服务器。


```
Access-Control-Request-Headers: <field-name>[, <field-name>]*
```



