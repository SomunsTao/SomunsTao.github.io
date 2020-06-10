---
title: Vue 07：第一个vue-cli项目
date: 2020-3-22
tags: Vue
categories: Vue
keywords: Vue
description: 前端体系、前后端分离
comments: true
---


​	

# Vue：第一个vue-cli项目

## 什么是vue-cli

vue-cli 官方提供的一个脚手架,用于快速生成一个 vue 的项目模板;

预先定义好的目录结构及基础代码，就好比咱们在创建 Maven 项目时可以选择创建一个骨架项目，这个骨架项目就是脚手架,我们的开发更加的快速;

**主要的功能:**

- 统一的目录结构
- 本地调试
- 热部署
- 单元测试
- 集成打包上线

## 需要的环境

- Node.js : <http://nodejs.cn/download/>
  安装就无脑下一步就好,安装在自己的环境目录下
- Git : <https://git-scm.com/downloads>
  镜像:<https://npm.taobao.org/mirrors/git-for-windows/>

**确认nodejs安装成功:**

- cmd 下输入 `node -v`,查看是否能够正确打印出版本号即可!
- cmd 下输入 `npm-v`,查看是否能够正确打印出版本号即可!

这个npm,就是一个软件包管理工具,就和linux下的apt软件安装差不多!

**安装 Node.js 淘宝镜像加速器（cnpm）**

这样子的话,下载会快很多~

```
# -g 就是全局安装
npm install cnpm -g

# 或使用如下语句解决 npm 速度慢的问题
npm install --registry=https://registry.npm.taobao.org
```

安装过程可能有点慢~,耐心等待!虽然安装了cnpm,但是尽量少用!

安装的位置:`C:\Users\Administrator\AppData\Roaming\npm`

![QQ截图20191023160802.png](https://blog.kuangstudy.com/usr/uploads/2019/10/3274148234.png)

**安装 vue-cli**

```
cnpm install vue-cli -g

# 测试是否安装成功
# 查看可以基于哪些模板创建 vue 应用程序，通常我们选择 webpack
vue list
```

![QQ截图20191023161055.png](https://blog.kuangstudy.com/usr/uploads/2019/10/1623428782.png)

## 第一个 vue-cli 应用程序

1. 创建一个Vue项目,我们随便建立一个空的文件夹在电脑上,我这里在D盘下新建一个目录`D:\Project\vue-study`;

2. 创建一个基于 webpack 模板的 vue 应用程序

   ```
   # 这里的 myvue 是项目名称，可以根据自己的需求起名
   vue init webpack myvue
   ```

   一路都选择no即可;

**说明:**

- Project name：项目名称，默认 回车 即可
- Project description：项目描述，默认 回车 即可
- Author：项目作者，默认 回车 即可
- Install vue-router：是否安装 vue-router，选择 n 不安装（后期需要再手动添加）
- Use ESLint to lint your code：是否使用 ESLint 做代码检查，选择 n 不安装（后期需要再手动添加）
- Set up unit tests：单元测试相关，选择 n 不安装（后期需要再手动添加）
- Setup e2e tests with Nightwatch：单元测试相关，选择 n 不安装（后期需要再手动添加）
- Should we run npm install for you after the project has been created：创建完成后直接初始化，选择 n，我们手动执行;运行结果!
  ![QQ截图20191023161857.png](https://blog.kuangstudy.com/usr/uploads/2019/10/1192113224.png)

### 初始化并运行

```
cd myvue
npm install
npm run dev
```

执行完成后,目录多了很多依赖

![QQ截图20191023162254.png](https://blog.kuangstudy.com/usr/uploads/2019/10/1102095439.png)

安装并运行成功后在浏览器输入：[http://localhost](http://localhost/):8080

![QQ截图20191023162439.png](https://blog.kuangstudy.com/usr/uploads/2019/10/2731297978.png)

**效果:**

![QQ截图20191023162508.png](https://blog.kuangstudy.com/usr/uploads/2019/10/3761367777.png)

## Vue-cli目录结构

我们用IDEA,open刚才的项目!

![QQ截图20191023162755.png](https://blog.kuangstudy.com/usr/uploads/2019/10/3943650778.png)

- build 和 config：WebPack 配置文件
- node_modules：用于存放 npm install 安装的依赖文件
- src： 项目源码目录
- static：静态资源文件
- .babelrc：Babel 配置文件，主要作用是将 ES6 转换为 ES5
- .editorconfig：编辑器配置
- eslintignore：需要忽略的语法检查配置文件
- .gitignore：git 忽略的配置文件
- .postcssrc.js：css 相关配置文件，其中内部的 module.exports 是 NodeJS 模块化语法
- index.html：首页，仅作为模板页，实际开发时不使用
- package.json：项目的配置文件
  - name：项目名称
  - version：项目版本
  - description：项目描述
  - author：项目作者
  - scripts：封装常用命令
  - dependencies：生产环境依赖
  - devDependencies：开发环境依赖

## src 目录

`src` 目录是项目的源码目录，所有代码都会写在这里

![QQ截图20191023164841.png](https://blog.kuangstudy.com/usr/uploads/2019/10/1887353409.png)

### main.js

项目的入口文件，我们知道所有的程序都会有一个入口

```
// The Vue build version to load with the `import` command
// (runtime-only or standalone) has been set in webpack.base.conf with an alias.
import Vue from 'vue'
import App from './App'

Vue.config.productionTip = false;

/* eslint-disable no-new */
new Vue({
  el: '#app',
  components: { App },
  template: '<App/>'
});
```

- `import Vue from 'vue'`：ES6 写法，会被转换成 require("vue"); （require 是 NodeJS 提供的模块加载器）
- `import App from './App'`：意思同上，但是指定了查找路径，./ 为当前目录
- `Vue.config.productionTip = false`：关闭浏览器控制台关于环境的相关提示
- `new Vue({...})`：实例化 Vue
  - `el: '#app'`：查找 index.html 中 id 为 app 的元素
  - `template: '<App/>'`：模板，会将 index.html 中 <div id="app"></div> 替换为 <App />
  - `components: { App }`：引入组件，使用的是 import App from './App' 定义的 App 组件;

### App.vue

```
<template>
  <div id="app">
    <img src="./assets/logo.png">
    <HelloWorld/>
  </div>
</template>

<script>
import HelloWorld from './components/HelloWorld'

export default {
  name: 'App',
  components: {
    HelloWorld
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

- template：HTML 代码模板，会替换 <App /> 中的内容
- import HelloWorld from './components/HelloWorld'：引入 HelloWorld 组件，用于替换 template 中的 <HelloWorld/>
- export default{...}：导出 NodeJS 对象，作用是可以通过 import 关键字导入
  - name: 'App'：定义组件的名称
  - components: { HelloWorld }：定义子组件

在hello,Vue中,关于 <style scoped> 的说明：CSS 样式仅在当前组件有效，声明了样式的作用域,是当前的界面私有的!