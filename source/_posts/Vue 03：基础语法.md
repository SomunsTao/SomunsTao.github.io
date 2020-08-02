---
title: Vue 03：基础语法
date: 2020-4-13
tags: Vue
categories: Vue
keywords: Vue
description: 前端体系、前后端分离
comments: true
---


​	

# Vue：基础语法

## v-bind

我们已经成功创建了第一个 Vue 应用！看起来这跟渲染一个字符串模板非常类似，但是 Vue 在背后做了大量工作。现在数据和 DOM 已经被建立了关联，所有东西都是响应式的。我们在控制台操作对象属性，界面可以实时更新！

我们还可以使用`v-bind`来绑定元素特性!

**上代码：**

```
<!DOCTYPE html>
<html xmlns:v-bind="http://www.w3.org/1999/xhtml">
<head>
    <meta charset="UTF-8">
    <title>狂神说Java</title>
    <script src="https://cdn.jsdelivr.net/npm/vue@2.5.21/dist/vue.js"></script>
</head>
<body>

<div id="app">
  <span v-bind:title="message">
    鼠标悬停几秒钟查看此处动态绑定的提示信息！
  </span>
</div>

<script type="text/javascript">
    var app = new Vue({
        el: '#app',
        data: {
            message: '页面加载于 ' + new Date().toLocaleString()
        }
    })
</script>
</body>
</html>
```

你看到的 v-bind 等被称为指令。指令带有前缀 v-，以表示它们是 Vue 提供的特殊特性。可能你已经猜到了，它们会在渲染的 DOM 上应用特殊的响应式行为。在这里，该指令的意思是：“将这个元素节点的 title 特性和 Vue 实例的 message 属性保持一致”。

如果你再次打开浏览器的 JavaScript 控制台，输入 app.message = '新消息'，就会再一次看到这个绑定了 title 特性的 HTML 已经进行了更新。

## v-if,v-else

什么是条件判断语句，就不需要我说明了吧（￣▽￣）,以下两个属性！

- `v-if`
- `v-else`

**上代码：**

```
<!DOCTYPE html>
<html>
<head>
    <meta charset="UTF-8">
    <title>狂神说Java</title>
    <script src="https://cdn.jsdelivr.net/npm/vue@2.5.21/dist/vue.js"></script>
</head>
<body>

<div id="vue">
    <h1 v-if="ok">YES</h1>
    <h1 v-else>NO</h1>
</div>

<script type="text/javascript">
    var vm = new Vue({
        el: '#vue',
        data: {
            ok: true
        }
    });
</script>
</body>
</html>
```

测试：

1. 在浏览器上运行，打开控制台！
2. 在控制台输入 `vm.ok = false` ，然后 回车，你会发现浏览器中显示的内容会直接变成 NO

注：使用 `v-*` 属性绑定数据是不需要 `双花括号` 包裹的

## v-else-if

- v-if
- v-else-if
- v-else

注：`===` 三个等号在 JS 中表示绝对等于（就是数据与类型都要相等）

**上代码：**

```
<!DOCTYPE html>
<html>
<head>
    <meta charset="UTF-8">
    <title>狂神说Java</title>
    <script src="https://cdn.jsdelivr.net/npm/vue@2.5.21/dist/vue.js"></script>
</head>
<body>

<div id="vue">
    <h1 v-if="type === 'A'">A</h1>
    <h1 v-else-if="type === 'B'">B</h1>
    <h1 v-else-if="type === 'C'">C</h1>
    <h1 v-else>who</h1>
</div>

<script type="text/javascript">
    var vm = new Vue({
        el: '#vue',
        data: {
            type: 'A'
        }
    });
</script>
</body>
</html>
```

测试：观察在控制台输入 vm.type = 'B'、'C'、'D' 的变化

## v-for

- v-for

格式说明：

```
<div id="vue">
    <li v-for="item in items">
        {{ item.message }}
    </li>
</div>
```

注：`items` 是数组，`item`是数组元素迭代的别名。我们之后学习的Thymeleaf模板引擎的语法和这个十分的相似！

**上代码：**

```
<!DOCTYPE html>
<html>
<head>
    <meta charset="UTF-8">
    <title>狂神说Java</title>
    <script src="https://cdn.jsdelivr.net/npm/vue@2.5.21/dist/vue.js"></script>
</head>
<body>

<div id="vue">
    <li v-for="item in items">
        {{ item.message }}
    </li>
</div>

<script type="text/javascript">
    var vm = new Vue({
        el: '#vue',
        data: {
            //items数组
            items: [
                {message: '狂神说Java'},
                {message: '狂神说前端'}
            ]
        }
    });
</script>
</body>
</html>
```

测试 ：在控制台输入 `vm.items.push({message: '狂神说运维'})` ，尝试追加一条数据，你会发现浏览器中显示的内容会增加一条 `狂神说运维`.

## v-on

`v-on` 监听事件

事件有Vue的事件、和前端页面本身的一些事件！我们这里的`click`是vue的事件，可以绑定到Vue中的`methods`中的方法事件！

**上代码：**

```
<!DOCTYPE html>
<html xmlns:v-on="">
<head>
    <meta charset="UTF-8">
    <title>狂神说Java</title>
    <script src="https://cdn.jsdelivr.net/npm/vue@2.5.21/dist/vue.js"></script>
</head>
<body>

<div id="vue">
    <!--在这里我们使用了 v-on 绑定了 click 事件，并指定了名为 sayHi 的方法-->
    <button v-on:click="sayHi">点我</button>
</div>


<script type="text/javascript">
    var vm = new Vue({
        el: '#vue',
        data: {
            message: 'Hello World'
        },
        // 方法必须定义在 Vue 实例的 methods 对象中
        methods: {
            sayHi: function (event) {
                // `this` 在方法里指向当前 Vue 实例
                alert(this.message);
            }
        }
    });
</script>
</body>
</html>
```

点击测试

Vue还有一些基本的使用方式，大家有需要的可以再跟着官方文档看看，因为这些基本的指令几乎我们都见过了，一通百通！掌握学习的方式！

[VUE](https://blog.kuangstudy.com/index.php/tag/Vue/)