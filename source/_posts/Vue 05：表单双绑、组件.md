---
title: Vue 05：表单双绑、组件
date: 2020-3-22
tags: Vue
categories: Vue
keywords: Vue
description: 前端体系、前后端分离
comments: true
---


​	

# Vue：表单双绑、组件

2019-10-23 / [前端](https://blog.kuangstudy.com/index.php/category/qianduan/) / [狂神说](https://blog.kuangstudy.com/index.php/author/1/)位置： [Home](https://blog.kuangstudy.com/) » [前端](https://blog.kuangstudy.com/index.php/category/qianduan/) » [本页](https://blog.kuangstudy.com/index.php/archives/587/)

## 什么是双向数据绑定

Vue.js 是一个 MVVM 框架，即数据双向绑定，即当数据发生变化的时候，视图也就发生变化，当视图发生变化的时候，数据也会跟着同步变化。这也算是 Vue.js 的精髓之处了。

值得注意的是，我们所说的数据双向绑定，一定是对于 UI 控件来说的，非 UI 控件不会涉及到数据双向绑定。单向数据绑定是使用状态管理工具的前提。如果我们使用 `vuex`，那么数据流也是单项的，这时就会和双向数据绑定有冲突。

### 为什么要实现数据的双向绑定

在 `Vue.js` 中，如果使用 `vuex`，实际上数据还是单向的，之所以说是数据双向绑定，这是用的 UI 控件来说，对于我们处理表单，Vue.js 的双向数据绑定用起来就特别舒服了。即两者并不互斥，在全局性数据流使用单项，方便跟踪；局部性数据流使用双向，简单易操作。

## 在表单中使用双向数据绑定

你可以用 `v-model` 指令在表单 `<input>`、`<textarea>` 及 `<select>` 元素上创建双向数据绑定。它会根据控件类型自动选取正确的方法来更新元素。尽管有些神奇，但 v-model 本质上不过是语法糖。它负责监听用户的输入事件以更新数据，并对一些极端场景进行一些特殊处理。

**注意：v-model 会忽略所有表单元素的 value、checked、selected 特性的初始值而总是将 Vue 实例的数据作为数据来源。你应该通过 JavaScript 在组件的 data 选项中声明初始值!**

### 单行文本

```
<!DOCTYPE html>
<html>
<head>
    <meta charset="UTF-8">
    <title>狂神说Java</title>
    <script src="https://cdn.jsdelivr.net/npm/vue@2.5.21/dist/vue.js"></script>
</head>
<body>

<!--我们这里希望，输入框的值和{}取值动态绑定，实时相同，我们就使用v-model绑定message-->
<div id="vue">
    单行文本：<input type="text" v-model="message" value="hello" />&nbsp;&nbsp;单行文本是：{{message}}
</div>

<script type="text/javascript">
    var vm = new Vue({
        el: '#vue',
        data: {
            message: "Hello Vue"
        }
    });
</script>

</body>
</html>
```

### 多行文本

```
<div id="vue">
    多行文本：<textarea v-model="message"></textarea>&nbsp;&nbsp;多行文本是：{{message}}
</div>

<script type="text/javascript">
    var vm = new Vue({
        el: '#vue',
        data: {
            message: "Hello Textarea"
        }
    });
</script>
```

### 单复选框

```
<div id="vue">
    单复选框：
    <input type="checkbox" id="checkbox" v-model="checked">
    &nbsp;&nbsp;
    <label for="checkbox">{{ checked }}</label>
</div>

<script type="text/javascript">
    var vm = new Vue({
        el: '#vue',
        data: {
            checked: false
        }
    });
</script>
```

### 多复选框

```
<div id="vue">
    多复选框：
    <input type="checkbox" id="jack" value="Jack" v-model="checkedNames">
    <label for="jack">Jack</label>
    <input type="checkbox" id="john" value="John" v-model="checkedNames">
    <label for="john">John</label>
    <input type="checkbox" id="mike" value="Mike" v-model="checkedNames">
    <label for="mike">Mike</label>
    <span>选中的值: {{ checkedNames }}</span>
</div>

<script type="text/javascript">
    var vm = new Vue({
        el: '#vue',
        data: {
            checkedNames: []
        }
    });
</script>
```

### 单选按钮

```
<div id="vue">
    单选按钮：
    <input type="radio" id="one" value="One" v-model="picked">
    <label for="one">One</label>
    <input type="radio" id="two" value="Two" v-model="picked">
    <label for="two">Two</label>
    <span>选中的值: {{ picked }}</span>
</div>

<script type="text/javascript">
    var vm = new Vue({
        el: '#vue',
        data: {
            picked: ''
        }
    });
</script>
```

### 下拉框

```
<div id="vue">
    下拉框：
    <select v-model="selected">
        <option disabled value="">请选择</option>
        <option>A</option>
        <option>B</option>
        <option>C</option>
    </select>
    <span>选中的值: {{ selected }}</span>
</div>

<script type="text/javascript">
    var vm = new Vue({
        el: '#vue',
        data: {
            selected: ''
        }
    });
</script>
```

**注意**：如果 `v-model` 表达式的初始值未能匹配任何选项，<select> 元素将被渲染为“未选中”状态。在 iOS 中，这会使用户无法选择第一个选项。因为这样的情况下，iOS 不会触发 change 事件。因此，更推荐像上面这样提供一个值为空的禁用选项。

## 什么是组件

组件是可复用的 `Vue` 实例，说白了就是一组可以重复使用的模板，跟 JSTL 的自定义标签、Thymeleaf 的 `th:fragment `等框架有着异曲同工之妙。通常一个应用会以一棵嵌套的组件树的形式来组织：

![vue-component.png](https://blog.kuangstudy.com/usr/uploads/2019/10/1384909422.png)

例如，你可能会有页头、侧边栏、内容区等组件，每个组件又包含了其它的像导航链接、博文之类的组件。

### 第一个 Vue 组件

注意：在实际开发中，我们并不会用以下方式开发组件，而是采用 vue-cli 创建 .vue 模板文件的方式开发，以下方法只是为了让大家理解什么是组件。

**使用 Vue.component() 方法注册组件,格式如下：**

```
<script type="text/javascript">
    // 先注册组件
    Vue.component('my-component-li', {
        template: '<li>Hello li</li>'
    });

    // 再实例化 Vue
    var vm = new Vue({
        el: '#vue'
    });
</script>

<div id="vue">
    <ul>
        <my-component-li></my-component-li>
    </ul>
</div>
```

说明：

- Vue.component()：注册组件
- my-component-li：自定义组件的名字
- template：组件的模板

### 使用 `props` 属性传递参数

像上面那样用组件没有任何意义，所以我们是需要传递参数到组件的，此时就需要使用 `props` 属性了！

**注意：默认规则下 props 属性里的值不能为大写；**

```
<script type="text/javascript">
    // 先注册组件
    Vue.component('my-component-li', {
        props: ['item'],
        template: '<li>Hello {{item}}</li>'
    });

    // 再实例化 Vue
    var vm = new Vue({
        el: '#vue',
        data: {
            items: ["张三", "李四", "王五"]
        }
    });
</script>

<div id="vue">
    <ul>
        <my-component-li v-for="item in items" v-bind:item="item"></my-component-li>
    </ul>
</div>
```

说明：

- `v-for="item in items"`：遍历 `Vue` 实例中定义的名为 `items` 的数组，并创建同等数量的组件
- `v-bind:item="item"`：将遍历的 `item` 项绑定到组件中 `props` 定义的名为 `item` 属性上；= 号左边的 item 为 props 定义的属性名，右边的为 `item in items` 中遍历的 item 项的值