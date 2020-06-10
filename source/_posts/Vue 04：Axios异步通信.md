---
title: Vue 04：Axios异步通信
date: 2020-3-22
tags: Vue
categories: Vue
keywords: Vue
description: 前端体系、前后端分离
comments: true
---


​	

# Vue：Axios异步通信

## 什么是Axios

Axios 是一个开源的可以用在浏览器端和 `NodeJS` 的异步通信框架，她的主要作用就是实现 AJAX 异步通信，其功能特点如下：

- 从浏览器中创建 `XMLHttpRequests`
- 从 node.js 创建 http 请求
- 支持 Promise API [JS中链式编程]
- 拦截请求和响应
- 转换请求数据和响应数据
- 取消请求
- 自动转换 JSON 数据
- 客户端支持防御 XSRF（跨站请求伪造）

GitHub：<https://github.com/axios/axios>

中文文档：<http://www.axios-js.com/>

### 为什么要使用 Axios

由于 `Vue.js` 是一个 视图层框架 并且作者（尤雨溪）严格准守 SoC （关注度分离原则），所以 `Vue.js` 并不包含 AJAX 的通信功能，为了解决通信问题，作者单独开发了一个名为 `vue-resource` 的插件，不过在进入 2.0 版本以后停止了对该插件的维护并推荐了 `Axios` 框架。少用jQuery，因为它操作Dom太频繁！

## 第一个 Axios 应用程序

咱们开发的接口大部分都是采用 JSON 格式，可以先在项目里模拟一段 JSON 数据，数据内容如下：创建一个名为 data.json 的文件并填入上面的内容，放在项目的根目录下

```
{
  "name": "狂神说Java",
  "url": "https://blog.kuangstudy.com",
  "page": 1,
  "isNonProfit": true,
  "address": {
    "street": "含光门",
    "city": "陕西西安",
    "country": "中国"
  },
  "links": [
    {
      "name": "bilibili",
      "url": "https://space.bilibili.com/95256449"
    },
    {
      "name": "狂伸说java",
      "url": "https://blog.kuangstudy.com"
    },
    {
      "name": "百度",
      "url": "https://www.baidu.com/"
    }
  ]
}
```

**测试代码**

```
<!DOCTYPE html>
<html lang="en" xmlns:v-bind="http://www.w3.org/1999/xhtml">
<head>
    <meta charset="UTF-8">
    <title>狂神说Java</title>
    <!--v-cloak 解决闪烁问题-->
    <style>
        [v-cloak] {
            display: none;
        }
    </style>
</head>
<body>

<div id="vue" v-cloak>
    <div>名称：{{info.name}}</div>
    <div>地址：{{info.address.country}}-{{info.address.city}}-{{info.address.street}}</div>
    <div>链接：<a v-bind:href="info.url" target="_blank">{{info.url}}</a> </div>
</div>

<!--引入 JS 文件-->
<script src="https://cdn.jsdelivr.net/npm/vue@2.5.21/dist/vue.js"></script>
<script src="https://unpkg.com/axios/dist/axios.min.js"></script>
<script type="text/javascript">
    var vm = new Vue({
        el: '#vue',
        data() {
            return {
                info: {
                    name: null,
                    address: {
                        country: null,
                        city: null,
                        street: null
                    },
                    url: null
                }
            }
        },
        mounted() { //钩子函数
            axios
                .get('data.json')
                .then(response => (this.info = response.data));
        }
    });
</script>

</body>
</html>
```

说明:

1. 在这里使用了 v-bind 将 a:href 的属性值与 Vue 实例中的数据进行绑定
2. 使用 axios 框架的 get 方法请求 AJAX 并自动将数据封装进了 Vue 实例的数据对象中
3. 我们在data中的数据结构必须要和`Ajax`响应回来的数据格式匹配！

## Vue的生命周期

官方文档：<https://cn.vuejs.org/v2/guide/instance.html#生命周期图示>

Vue 实例有一个完整的生命周期，也就是从开始创建、初始化数据、编译模板、挂载 DOM、渲染→更新→渲染、卸载等一系列过程，我们称这是 Vue 的生命周期。通俗说就是 Vue 实例从创建到销毁的过程，就是生命周期。

在 Vue 的整个生命周期中，它提供了一系列的事件，可以让我们在事件触发时注册 JS 方法，可以让我们用自己注册的 JS 方法控制整个大局，在这些事件响应方法中的 this 直接指向的是 Vue 的实例。

![vue-life.jpg](https://blog.kuangstudy.com/usr/uploads/2019/10/1579484219.jpg)