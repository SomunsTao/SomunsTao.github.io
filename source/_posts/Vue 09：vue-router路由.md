---
title: Vue 09：vue-router路由
date: 2020-3-22
tags: Vue
categories: Vue
keywords: Vue
description: 前端体系、前后端分离
comments: true
---


​	

# Vue：vue-router路由

### 说明

学习的时候，尽量的打开官方的文档

Vue Router 是 Vue.js 官方的路由管理器。它和 Vue.js 的核心深度集成，让构建单页面应用变得易如反掌。包含的功能有：

- 嵌套的路由/视图表
- 模块化的、基于组件的路由配置
- 路由参数、查询、通配符
- 基于 Vue.js 过渡系统的视图过渡效果
- 细粒度的导航控制
- 带有自动激活的 CSS class 的链接
- HTML5 历史模式或 hash 模式，在 IE9 中自动降级
- 自定义的滚动条行为

### 安装

**基于第一个vue-cli进行测试学习;先查看node_modules中是否存在 vue-router**

vue-router 是一个插件包，所以我们还是需要用 npm/cnpm 来进行安装的。打开命令行工具，进入你的项目目录，输入下面命令。

```
npm install vue-router --save-dev
```

如果在一个模块化工程中使用它，必须要通过 Vue.use() 明确地安装路由功能：

```
import Vue from 'vue'
import VueRouter from 'vue-router'

Vue.use(VueRouter);
```

## 测试

1、先删除没有用的东西
2、`components`目录下存放我们自己编写的组件
3、定义一个`Content.vue` 的组件

```
<template>
  <div>
 <h1>内容页</h1>
  </div>
</template>

<script>
 export default {
     name: "Content"
 }
</script>
```

4、 **安装路由,在src目录下,新建一个文件夹 : router,专门存放路由**

```
import Vue from 'vue'
// 导入路由插件
import Router from 'vue-router'
// 导入上面定义的组件
import Content from '../components/Content'
import main from '../components/main'
// 安装路由
Vue.use(Router);
// 配置路由
export default new Router({
  routes: [
    {
      // 路由路径
      path: '/content',
      // 路由名称
      name: 'Content',
      // 跳转到组件
      component: Content
    }, {
      // 路由路径
      path: '/main',
      // 路由名称
      name: 'main',
      // 跳转到组件
      component: main
    }
  ]
});
```

5、在`main.js` 中配置路由

```
import Vue from 'vue'
import App from './App'

// 导入上面创建的路由配置目录
import router from './router'

//来关闭生产模式下给出的提示
Vue.config.productionTip = false;

new Vue({
  el: '#app',
  // 配置路由
  router,
  components: { App },
  template: '<App/>'
});
```

6、在`App.vue`中使用路由

```
<template>
  <div id="app">
    <!--
      router-link： 默认会被渲染成一个 <a> 标签，to 属性为指定链接
      router-view： 用于渲染路由匹配到的组件
    -->
    <router-link to="/">首页</router-link>
    <router-link to="/content">内容</router-link>
    <router-view></router-view>
  </div>
</template>

<script>
export default {
  name: 'App'
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

启动测试一下 ： `npm run dev`

**练习： 在现有的基础上，在增加一个路由组件，优化一下！**