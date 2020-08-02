---
title: Vue 02：MVVM模式和第一个Vue程序
date: 2020-4-12
tags: Vue
categories: Vue
keywords: Vue
description: 前端体系、前后端分离MVVM
---


​	

# Vue：MVVM模式和第一个Vue程序

## 什么是 MVVM

MVVM（Model-View-ViewModel）是一种软件架构设计模式，由微软 WPF（用于替代 WinForm，以前就是用这个技术开发桌面应用程序的）和 Silverlight（类似于 Java Applet，简单点说就是在浏览器上运行的 WPF） 的架构师 Ken Cooper 和 Ted Peters 开发，是一种简化用户界面的**事件驱动编程方式**。由 John Gossman（同样也是 WPF 和 Silverlight 的架构师）于 2005 年在他的博客上发表。

MVVM 源自于经典的 MVC（Model-View-Controller）模式。MVVM 的核心是 ViewModel 层，负责转换 Model 中的数据对象来让数据变得更容易管理和使用，其作用如下：

- 该层向上与视图层进行双向数据绑定
- 向下与 Model 层通过接口请求进行数据交互
  ![20191017111649.png](https://blog.kuangstudy.com/usr/uploads/2019/10/1897504213.png)

MVVM 已经相当成熟了，主要运用但不仅仅在网络应用程序开发中。当下流行的 MVVM 框架有 `Vue.js`，`AngularJS` 等。

## 为什么要使用 MVVM

MVVM 模式和 MVC 模式一样，主要目的是分离视图（View）和模型（Model），有几大好处

- **低耦合**： 视图（View）可以独立于 Model 变化和修改，一个 ViewModel 可以绑定到不同的 View 上，当 View 变化的时候 Model 可以不变，当 Model 变化的时候 View 也可以不变。
- **可复用**： 你可以把一些视图逻辑放在一个 ViewModel 里面，让很多 View 重用这段视图逻辑。
- **独立开发**： 开发人员可以专注于业务逻辑和数据的开发（ViewModel），设计人员可以专注于页面设计。
- **可测试**： 界面素来是比较难于测试的，而现在测试可以针对 ViewModel 来写。

## MVVM 的组成部分

![20191017111955.png](https://blog.kuangstudy.com/usr/uploads/2019/10/1552878682.png)

### View

View 是视图层，也就是用户界面。前端主要由 `HTML` 和 `CSS` 来构建，为了更方便地展现 `ViewModel` 或者 `Model` 层的数据，已经产生了各种各样的前后端模板语言，比如 FreeMarker、Thymeleaf 等等，各大 MVVM 框架如 Vue.js，AngularJS，EJS 等也都有自己用来构建用户界面的内置模板语言。

### Model

Model 是指数据模型，泛指后端进行的各种业务逻辑处理和数据操控，主要围绕数据库系统展开。这里的难点主要在于需要和前端约定统一的 `接口规则`

### ViewModel

ViewModel 是由前端开发人员组织生成和维护的视图数据层。在这一层，前端开发者对从后端获取的 Model 数据进行转换处理，做二次封装，以生成符合 View 层使用预期的视图数据模型。

需要注意的是 ViewModel 所封装出来的数据模型包括视图的状态和行为两部分，而 Model 层的数据模型是只包含状态的

- 比如页面的这一块展示什么，那一块展示什么这些都属于视图状态（展示）
- 页面加载进来时发生什么，点击这一块发生什么，这一块滚动时发生什么这些都属于视图行为（交互）

视图状态和行为都封装在了 ViewModel 里。这样的封装使得 ViewModel 可以完整地去描述 View 层`。由于实现了双向绑定，ViewModel 的内容会实时展现在 View 层，这是激动人心的，因为前端开发者再也不必低效又麻烦地通过操纵 DOM 去更新视图。

MVVM 框架已经把最脏最累的一块做好了，我们开发者只需要处理和维护 ViewModel，更新数据视图就会自动得到相应更新，真正实现 `事件驱动编程`。

View 层展现的不是 `Model` 层的数据，而是 `ViewModel` 的数据，由 `ViewModel` 负责与 `Model` 层交互，这就**完全解耦了 View 层和 Model 层，这个解耦是至关重要的，它是前后端分离方案实施的重要一环。**

## Vue

Vue (读音 /vjuː/，类似于 view) 是一套用于构建用户界面的渐进式框架，发布于 2014 年 2 月。与其它大型框架不同的是，Vue 被设计为可以自底向上逐层应用。Vue 的核心库只关注视图层，不仅易于上手，还便于与第三方库（如：vue-router，vue-resource，vuex）或既有项目整合。

### MVVM 模式的实现者

- Model：模型层，在这里表示 JavaScript 对象
- View：视图层，在这里表示 DOM（HTML 操作的元素）
- ViewModel：连接视图和数据的中间件，Vue.js 就是 MVVM 中的 ViewModel 层的实现者

在 MVVM 架构中，是不允许 数据 和 视图 直接通信的，只能通过 ViewModel 来通信，而 ViewModel 就是定义了一个 Observer 观察者

- ViewModel 能够观察到数据的变化，并对视图对应的内容进行更新
- ViewModel 能够监听到视图的变化，并能够通知数据发生改变

至此，我们就明白了，Vue.js 就是一个 MVVM 的实现者，他的核心就是实现了 DOM 监听 与 数据绑定

### 为什么要使用 Vue.js

- 轻量级，体积小是一个重要指标。Vue.js 压缩后有只有 20多kb （Angular 压缩后 56kb+，React 压缩后 44kb+）
- 移动优先。更适合移动端，比如移动端的 Touch 事件
- 易上手，学习曲线平稳，文档齐全
- 吸取了 Angular（模块化）和 React（虚拟 DOM）的长处，并拥有自己独特的功能，如：计算属性
- 开源，社区活跃度高
- ......

## 第一个Vue程序

【说明】IDEA 可以安装 Vue 的插件！

注意：Vue 不支持 IE8 及以下版本，因为 Vue 使用了 IE8 无法模拟的 ECMAScript 5 特性。但它支持所有兼容 ECMAScript 5 的浏览器。

### 下载地址

- 开发版本
  - 包含完整的警告和调试模式：<https://vuejs.org/js/vue.js>
  - 删除了警告，30.96KB min + gzip：<https://vuejs.org/js/vue.min.js>
- CDN
  - `<script src="https://cdn.jsdelivr.net/npm/vue@2.5.21/dist/vue.js"></script>`
  - `<script src="https://cdn.jsdelivr.net/npm/vue@2.5.21/dist/vue.min.js"></script>`

### 代码编写

Vue.js 的核心是实现了 MVVM 模式，她扮演的角色就是 ViewModel 层，那么所谓的第一个应用程序就是展示她的 数据绑定 功能，操作流程如下：

**1、创建一个 HTML 文件**

```
<!DOCTYPE html>
<html>
<head>
    <meta charset="UTF-8">
    <title>狂神说</title>
</head>
<body>

</body>
</html>
```

**2、引入 Vue.js**

```
<script src="https://cdn.jsdelivr.net/npm/vue@2.5.21/dist/vue.js"></script>
```

**3、创建一个 Vue 的实例**

```
<script type="text/javascript">
    var vm = new Vue({
        el: '#vue',
        data: {
            message: 'Hello Vue!'
        }
    });
</script>
```

说明:

- `el:'#vue'`：绑定元素的 ID
- `data:{message:'Hello Vue!'}`：数据对象中有一个名为 message 的属性，并设置了初始值 Hello Vue!

**4、将数据绑定到页面元素**

```
<div id="vue">
    {{message}}
</div>
```

说明：只需要在绑定的元素中使用 双花括号 将 Vue 创建的名为 message 属性包裹起来，即可实现数据绑定功能，也就实现了 ViewModel 层所需的效果，是不是和 EL 表达式非常像？

### 完整的 HTML

```
<!DOCTYPE html>
<html>
<head>
    <meta charset="UTF-8">
    <title>第一个 Vue 应用程序</title>
</head>
<body>

<!--View-->
<div id="vue">
     {{message}}
</div>

<script src="https://cdn.jsdelivr.net/npm/vue@2.5.21/dist/vue.js"></script>
<script type="text/javascript">
    // var vm = new Vue({}); //ViewModel
    var vm = new Vue({
        el: '#vue',
        data: { //Model
            message: 'Hello Vue!'
        }
    });
</script>
</body>
</html>
```

### 测试

为了能够更直观的体验 Vue 带来的数据绑定功能，我们需要在浏览器测试一番，操作流程如下：

1、在浏览器上运行第一个 Vue 应用程序，进入 开发者工具

2、在控制台输入 vm.message = 'Hello World' ，然后 回车，你会发现浏览器中显示的内容会直接变成 Hello World

此时就可以在控制台直接输入 vm.message 来修改值，中间是可以省略 data 的，在这个操作中，我并没有主动操作 DOM，就让页面的内容发生了变化，这就是借助了 Vue 的 数据绑定 功能实现的；MVVM 模式中要求 ViewModel 层就是使用 观察者模式 来实现数据的监听与绑定，以做到数据与视图的快速响应。

