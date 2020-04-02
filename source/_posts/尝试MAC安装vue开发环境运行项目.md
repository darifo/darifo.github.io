---
title: 尝试MAC安装vue开发环境运行项目
date: 2019-01-15 11:56:33
categories: 
- 经验总结
tags:
- Vue
- elementUI
---





1、安装nodejs（使用mac包管理工具，没有先安装一下）


```
brew install nodejs 
```
检测安装成功


```
node -v
```

```
npm -v
```



2、安装vue脚手架（自动搭建vue项目框架的工具）


```
npm install -g vue-cli
```

检测是否安装成功：（ V 要大写 ）

```
vue -V
```
成功输出vue版本号



3、安装webpack


```
npm install webpack -g
```

4、创建一个基于webpack模版的vue项目


```
vue init webpack project_name
```

5、启动vue项目

进入目录执行


```
npm install
```

然后执行


```
npm run dev
```


成功后，默认运行在：http://localhost:8080/#/

如图：

![image](/images/20200402115726.png)



## 其他


安装 element-UI 支持


```
npm i element-ui -S
```

引入

main.js中添加
```
import ElementUI from 'element-ui';
import 'element-ui/lib/theme-chalk/index.css';

Vue.use(ElementUI);
```

使用

页面或组件模版部分添加

```
<el-row>
  <el-button>默认按钮</el-button>
  <el-button type="primary">主要按钮</el-button>
  <el-button type="success">成功按钮</el-button>
  <el-button type="info">信息按钮</el-button>
  <el-button type="warning">警告按钮</el-button>
  <el-button type="danger">危险按钮</el-button>
</el-row>
```

![image](/images/20200402115742.png)

可以看出，按钮样式已经正常渲染出来了



