---
title: Vue 06：计算属性、内容分发、自定义事件
date: 2020-3-22
tags: Vue
categories: Vue
keywords: Vue
description: 前端体系、前后端分离
comments: true
---

# Vue：计算属性、内容分发、自定义事件



## 什么是计算属性

计算属性的重点突出在 `属性` 两个字上（属性是名词），首先它是个 `属性` 其次这个属性有 `计算` 的能力（计算是动词），这里的 `计算` 就是个函数；简单点说，它就是一个能够将计算结果缓存起来的属性（将行为转化成了静态的属性），仅此而已；可以想象为缓存！

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
    <!--注意，一个是方法，一个是属性-->
    <p>调用当前时间的方法：{{currentTime1()}}</p>
    <p>当前时间的计算属性：{{currentTime2}}</p>
</div>

<script type="text/javascript">
    var vm = new Vue({
        el: '#vue',
        data: {
            message: 'Hello Vue'
        },
        methods: {
            currentTime1: function () {
                return Date.now();
            }
        },
        computed: {
            //currentTime2 ，这是一个属性！不是方法
            currentTime2: function () {
                this.message;
                return Date.now();
            }
        }
    });
</script>
</body>
</html>
```

**注意：methods 和 computed 里的东西不能重名**

**说明：**

- methods：定义方法，调用方法使用 currentTime1()，需要带括号
- computed：定义计算属性，调用属性使用 currentTime2，不需要带括号；this.message 是为了能够让 currentTime2 观察到数据变化而变化
- 如何在方法中的值发生了变化，则缓存就会刷新！可以在控制台使用 `vm.message="qinjiang"`,改变下数据的值，再次测试观察效果！

**结论：**

调用方法时，每次都需要进行计算，既然有计算过程则必定产生系统开销，那如果这个结果是不经常变化的呢？此时就可以考虑将这个结果缓存起来，采用计算属性可以很方便的做到这一点,**计算属性的主要特性就是为了将不经常变化的计算结果进行缓存，以节约我们的系统开销;**

## 内容分发

在 `Vue.js` 中我们使用 `<slot>` 元素作为承载分发内容的出口，作者称其为 插槽，可以应用在组合组件的场景中;

### 测试

比如准备制作一个待办事项组件（todo），该组件由待办标题（todo-title）和待办内容（todo-items）组成，但这三个组件又是相互独立的，该如何操作呢？

**第一步: 定义一个待办事项的组件**

```
<div id="vue">
    <todo></todo>
</div>

<script type="text/javascript">
    Vue.component('todo', {
        template: '<div>\
                    <div>待办事项</div>\
                    <ul>\
                        <li>学习狂神说Java</li>\
                    </ul>\
               </div>'
    });
</script>
```

**第二步: 我们需要让,待办事项的标题和值实现动态绑定,怎么做呢? 我们可以留出一个插槽!**

1-将上面的代码留出一个插槽,即 slot

```
Vue.component('todo', {
    template: '<div>\
                    <slot name="todo-title"></slot>\
                    <ul>\
                        <slot name="todo-items"></slot>\
                    </ul>\
               </div>'
});
```

2-定义一个名为 todo-title 的待办标题组件 和 todo-items 的待办内容组件

```
Vue.component('todo-title', {
    props: ['title'],
    template: '<div>{{title}}</div>'
});
```

```
//这里的index,就是数组的下标,使用for循环遍历的时候,可以循环出来!
Vue.component('todo-items', {
    props: ['item', 'index'],
    template: '<li>{{index + 1}}. {{item}}</li>'
});
```

3-实例化 Vue 并初始化数据

```
var vm = new Vue({
    el: '#vue',
    data: {
        todoItems: ['狂神说Java', '狂神说运维', '狂神说前端']
    }
});
```

4-将这些值,通过插槽插入

```
<div id="vue">
    <todo>
        <todo-title slot="todo-title" title="秦老师系列课程"></todo-title>
        <todo-items slot="todo-items" v-for="(item, index) in todoItems" v-bind:item="item" v-bind:index="index" :key="index"></todo-items>
    </todo>
</div>
```

说明:我们的 todo-title 和 todo-items 组件分别被分发到了 todo 组件的 todo-title 和 todo-items 插槽中

## 自定义事件

通过以上代码不难发现，数据项在 Vue 的实例中，但删除操作要在组件中完成，那么组件如何才能删除 Vue 实例中的数据呢？此时就涉及到参数传递与事件分发了，Vue 为我们提供了自定义事件的功能很好的帮助我们解决了这个问题；使用 this.$emit('自定义事件名', 参数)，操作过程如下:

1-在vue的实例中,增加了 methods 对象并定义了一个名为 removeTodoItems 的方法

```
    var vm = new Vue({
        el: '#vue',
        data: {
            title: "秦老师系列课程1",
            todoItems: ['狂神说Java', '狂神说运维', '狂神说前端']
        },
        methods: {
            // 该方法可以被模板中自定义事件触发
            removeTodoItems: function (index) {
                console.log("删除 " + this.todoItems[index] + " 成功");
                // splice() 方法向/从数组中添加/删除项目，然后返回被删除的项目，其中 index 为添加/删除项目的位置，1 表示删除的数量
                this.todoItems.splice(index, 1);
            }
        }
    });
```

2-修改 todo-items 待办内容组件的代码,增加一个删除按钮,并且绑定事件!

```
    Vue.component('todo-items', {
        props: ['item', 'index'],
        template: '<li>{{index + 1}}. {{item}}  <button @click="remove_component">删除</button></li>',
        methods: {
            remove_component: function (index) {
                // 这里的 remove 是自定义事件的名称，需要在 HTML 中使用 v-on:remove 的方式指派
                this.$emit('remove', index);
            }
        }
    });
```

3-修改 todo-items 待办内容组件的 HTML 代码,增加一个自定义事件,比如叫 remove,可以和组件的方法绑定,然后绑定到vue的方法中!

```
<!--增加了 v-on:remove="removeTodoItems(index)" 自定义事件，该事件会调用 Vue 实例中定义的名为 removeTodoItems 的方法-->
<todo-items slot="todo-items" v-for="(item, index) in todoItems"
            v-bind:item="item" v-bind:index="index" :key="index"
            v-on:remove="removeTodoItems(index)"></todo-items>
```

### 逻辑理解

![20191023150103.png](https://blog.kuangstudy.com/usr/uploads/2019/10/2041589231.png)

## Vue 入门小结

核心 : 数据驱动 , 组件化
优点 : 借鉴了 AngulaJS 的模块化开发 和 React 的虚拟Dom , 虚拟Dom就是把Dom操作放到内存中执行;

常用的属性:

- v-if
- v-else-if
- v-else
- v-for
- v-on 绑定事件 , 简写`@`
- v-model 数据双向绑定
- v-bind 给组件绑定参数,简写 `:`

组件化:

- 组合组件 slot 插槽
- 组件内部绑定事件需要使用到 `this.$emit("事件名",参数)`;
- 计算属性的特色,缓存计算数据

遵循SoC 关注度分离原则,Vue是纯粹的视图框架,并不包含,比如Ajax之类的通信功能,为了解决通信问题,我们需要使用Axios 框架做异步通信;

### 说明

Vue的开发都是要基于NodeJS, 实际开发采用 vue-cli脚手架开发,vue-router 路由,vuex做状态管理; Vue UI,界面我们一般使用 ElementUI(饿了么出品),或者ICE(阿里巴巴出品!)来快速搭建前端项目~

官网:

- <https://element.eleme.cn/#/zh-CN>
- <https://ice.work/>