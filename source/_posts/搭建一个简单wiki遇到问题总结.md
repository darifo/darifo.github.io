---
title: 搭建一个简单wiki遇到问题总结
date: 2019-01-20 11:42:28
categories: 
- 经验总结
tags:
- Vue
- axios
- elementUI
- mavonEditor
---



## 概述

> 使用技术框架：vue2 + vue-router + axios + mavonEditor + elementUI + ThinkPHP5


---

主要功能包括：

- 新增分类菜单
- 新增markdown文档
- 编辑文档
- 删除文档
- 结构化查看文档


---

### 涉及技术点：

##### 前端
- 无限级分类数据获取及菜单渲染
- 前端路由跳转及参数获取
- axios异步数据请求
- 无状态刷新页面
- localStorage本地数据存取
- 表单数据验证
- 弹窗消息管理
- 数据loading效果控制
- 组件化父子组件通讯
- 双向数据绑定
- 组件生命周期
- mavonEditor
- 接口数据格式转换

##### 后端

- 递归方式实现无限级菜单数据查询


##### 数据表设计


- 单表多功能数据结构



---




### 搭建架子



##### 前端架子


在之前的一篇文档《尝试MAC安装vue开发运行环境》所完成的工作基础上，

我们还需要进入已经能运行的项目目录，安装一些插件：


路由模块：==vue-router==


```
npm run vue-router --save
```

请求模块：==axios==

```
npm run axios --save
```

安装模块：==mavonEditor==


```
npm install mavon-editor --save
```

安装模块：==elementUI== （我之前已经安装好）


```
npm i element-ui -S
```

##### 后端架子

ThinkPHP框架，创建一个api模块，新建controller


##### 数据表设计


字段名称 | 字段类型 | 字段长度 | 是否为空 | 是否主键 | 默认值 | 字段说明 
--- | --- | --- |--- | --- | --- | --- |
    |     |     |    |     |     |     |
    |     |     |    |     |     |     |
    |     |     |    |     |     |     |
    |     |     |    |     |     |     |
    |     |     |    |     |     |     |
    |     |     |    |     |     |     |
    |     |     |    |     |     |     |
    |     |     |    |     |     |     |


---


### 编码


==main.js==


```js
// 引入vue模块
import Vue from 'vue'
import App from './App'

// 引入路由模块
import router from './router'

// 引入axios网络请求模块
import axios from 'axios'
import VueAxios from 'vue-axios'

// 引入elementUI组件
import ElementUI from 'element-ui'
import 'element-ui/lib/theme-chalk/index.css'

// 引入markdown编辑器mavonEditor
import mavonEditor from 'mavon-editor'
import 'mavon-editor/dist/css/index.css'

// 引入自定义工具扩展库
import utils from './utils'

// 注入vue
Vue.use(mavonEditor)
Vue.use(VueAxios,axios);
Vue.use(ElementUI);

// axios统一接口地址配置
axios.defaults.baseURL = ""

Vue.config.productionTip = false

/* eslint-disable no-new */
new Vue({
  el: '#app',
  router,
  components: { App },
  template: '<App/>'
})
```

==App.vue==


```html
<template>
  <div id="app">
    <!-- <img src="./assets/logo.png"> -->
    <router-view v-if="isRouterAlive"></router-view>
  </div>
</template>

<script>
export default {
  name: 'App',
	provide (){
     return {
       reload:this.reload
     }
  },
  data(){
    return {
       isRouterAlive:true
    }
},
	methods:{
		reload (){
       this.isRouterAlive = false
       this.$nextTick(function(){
          this.isRouterAlive = true
       })
    },
	}
}
</script>

<style>
#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>

```

==router/index.js==


```js
import Vue from 'vue'
import Router from 'vue-router'

// =========组件引入=========
import DocumentWiki from '@/components/document/wiki'
import DocumentWikiDetail from '@/components/document/wiki_detail'
import DocumentWikiAdd from '@/components/document/wiki_add'
import DocumentWikiEditor from '@/components/document/wiki_editor'

Vue.use(Router)

export default new Router({
  routes: [
	{
	  path: '/document/wiki',
	  name: 'DocumentWiki',
	  component: DocumentWiki
	},
	{
	  path: '/document/wiki_detail',
	  name: 'DocumentWikiDetail',
	  component: DocumentWikiDetail
	},
	{
	  path: '/document/wiki_add',
	  name: 'DocumentWikiAdd',
	  component: DocumentWikiAdd
	},
	{
	  path: '/document/wiki_editor',
	  name: 'DocumentWikiEditor',
	  component: DocumentWikiEditor
	},
  ]
})

```





