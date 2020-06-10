---
title: 快速上手前端框架layui
date: 2020-3-21
tags: UI
categories: 前端UI
keywords: 前端
description:  layui,是一款采用自身模块规范编写的前端 UI框架
---



# 快速上手前端框架layui

layui（谐音：类UI) 是一款采用自身模块规范编写的前端 UI 框架，遵循原生 HTML/CSS/JS 的书写与组织形式，门槛极低，拿来即用。

![img](https://img.php.cn/upload/article/000/000/039/5dce1b98bb428407.jpg)

**一、介绍**

**在使用layui之前，我们先要了解一下layui是什么？**

> 我觉得用作者贤心的一句话来概括就好了：为后端程序员设计的前端框架。
>
> 更加详细的描述是：这是一个封装了各种css和js、Ajax等等的前端框架，其封装程度之高，有时甚至对程序员来说不大友好。但对于前端技术一般的人来说，layui不失为一个好的工具。

**二、开始使用layui**

使用方式：下载后导入项目，然后引用即可

先要引用jquery，然后再引用layui.js和layui.css。

**为什么一定要本地呢？没有CDN？**

> 上面说了，layui封装得太“好”了，程序员的自主性受到限制，这个时候需要修改layui的源码，比如css的样式——这也是layui的正确用法，而不只是简单地使用。

layui的模块：layui是模块化的，包括form，layer，laydate，laypage等等模块，正是这些模块组成了完整的layui。使用layui的时候，需要指明自己使用的模块。

开始使用layui：

**三、layui表单**

下面以HTML中最常见的form表单来演示layui的使用。

html部分：

javscript部分：

效果图：

![1.jpg](https://img.php.cn/upload/image/312/187/705/1573787868677711.jpg)

**四、layui弹出层**

下面讲述一下弹出层，弹出可以说是一个很常见的东西了，但基础的HTML/JS只有丑陋的alert("")方法，layui包含了一个叫做layer的弹出层模块。

**使用layer的两种方式：**

> 一、像上面使用form模块一样，layui.use声明，然后在use后面的function里使用；
>
> 二、导入独立的layer模块文件，然后就可以直接使用；

关于第一种方式不予讨论，这里介绍一下第二种方式。

首先，从layer官网下载layer的文件，解压并放入自己的项目下，然后<script th:src="@{/layer/layer.js}"></script>类似这样的形式引入layer.js文件。

Example：

效果图：

![2.jpg](https://img.php.cn/upload/image/155/450/605/1573787968361625.jpg)

layer不仅仅可以弹出提示框，还可以做到一些有趣且实用的动态效果，甚至可以加载一个弹出的HTML界面出来。

**五、layui文件上传**

下面介绍一下layui的文件上传，即upload模块

后端（java-spring-controller类中）：

upload上传接口和返回值：

效果图：

![3.jpg](https://img.php.cn/upload/image/339/934/904/1573788062862810.jpg)

layui的upload模块能够在前端进行配置文件大小、格式、预览，还可以做到批量上传、重传功能。

**六、layui分页**

在网站中也经常会用到分页，后端的分页是容易实现的，但对于前端来说就不是那么理想了。layui提供了自己的分页模块——laypage。

如上，分页跳转的事件是在jump中进行的，在里面编写AJAX请求，通过jump的obj参数获得page和limit参数，然后在请求执行完毕并返回数据后进行处理即可。

**七、layui数据表格**

表格时常见的功能，但js拼接HTML表格算是一件比较繁琐且容易出错的事情。

效果图：

![4.jpg](https://img.php.cn/upload/image/117/971/582/1573788203854321.jpg)

更多layui知识请关注[layui框架](https://www.php.cn/layui/)。

以上就是快速上手前端框架layui的详细内容，更多请关注php中文网其它相关文章！