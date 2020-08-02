---
title: Element-ui 使用
date: 2020-3-22
tags: UI
categories: 前端UI
keywords: 前端
description:  Element-ui,是一款采用自身模块规范编写的前端 UI框架
---



# [Element-ui 使用详细介绍](https://www.cnblogs.com/itzlg/p/11976613.html)

一.后台搭建

使用 [vue-admin-template](https://github.com/PanJiaChen/vue-admin-template/blob/master/README-zh.md) 来快速搭建后台管理,它包含了 Element UI & axios & iconfont & permission control & lint，这些搭建后台必要的东西。

Element是（饿了么团队）基于MVVM框架Vue开源出来的一套前端ui组件。官网： [element-ui](https://element.eleme.io/#/zh-CN/component/installation)

#### 1.快速搭建

若用vue官网提供的脚手架需要写跟多代码，比如登录界面、主界面菜单等。可以使用 vue-admin-template ，在此基础上开发，而且github上配有详细的模板说明，这样可以节省开发时间。

1. 下载vue-admin-template(vue 2.0)进行解压

2. 在命令提示符切换到该目录，输入命令安装所有依赖：

   > cnpm install

3. 运行

   > npm run dev

#### 2.工程结构

| 目录名称   | 存储内容         |
| ---------- | ---------------- |
| build      | 构建工程相关脚本 |
| config     | 配置相关         |
| src        | 工程源码         |
| static     | 静态资源         |
| src/api    | 访问后端API      |
| src/utils  | 工具类           |
| src/views  | 路由组件         |
| src/router | 路由             |

#### 3.项目初始化

1. 关闭语法规范性检查
   修改config/index.js : useEslint = false

2. 国际化设置
   修改main.js :
   import locale from 'element‐ui/lib/locale/lang/en' --> import locale from 'elemen‐ui/lib/locale/lang/zh‐CN'

3. 与easy-mock对接
   修改config下dev.env.js中BASE_API为easy-mock的Base URL，easy-mock最好部署到服务器上

   ```
   BASE_API: '"http://192.168.91.136:7300/mock/5af314a4c612520d0d7650c7"',
   ```

   easy-mock添加登录认证模拟数据
   地址： /user/login
   提交方式：post
   内容：

   ```
   {
   	"code": 20000,
   	"data": {
   		"token": "admin"
   	}
   }
   ```

   添加返回用户信息url模拟数据
   地址：/user/info
   提交方式：get
   内容：

   ```
   {
     	"code": 20000,
     	"data": {
       	"roles": ["admin"],
       	"role": ["admin"],
       	"name": "admin",
       	"avatar": "https://wpimg.wallstcn.com/f778738c‐e4f8‐4870‐b634‐56703b4acafe.gif"
    	 }
   }
   ```

#### 4.更改标题与菜单

1. 修改index.html的标题为“后台管理系统”，修改后浏览器自动刷新。

2. 修改src/router下的index.js中constantRouterMap的内容

   ```javascript
   export const constantRoutes = [
     {
       path: '/login',
       component: () => import('@/views/login/index'),
       hidden: true
     },
   
     {
       path: '/404',
       component: () => import('@/views/404'),
       hidden: true
     },
   
     {
       path: '/',
       component: Layout,
       // redirect: '/dashboard',
       redirect: '/hostmanager/server',   //登录后重定向到服务器信息页面
       children: [{
         path: 'dashboard',
         name: 'Dashboard',
         component: () => import('@/views/dashboard/index'),
         meta: { title: 'Dashboard', icon: 'dashboard' }
       }]
     },
   
     {
       path: '/hostmanager',
       component: Layout,
       redirect: '/hostmanager/list',
       name: 'hostmanager',
       meta: { title: '主机管理', icon: 'example' },
       children: [
         {
           path: 'server',
           name: 'Server',
           component: () => import('@/views/server/index'),
           meta: { title: '服务器信息', icon: 'form' }
         },
         {
           path: 'list',
           name: 'List',
           component: () => import('@/views/list/index'),
           meta: { title: '主机列表', icon: 'table' }
         },
         {
           path: 'task',
           name: 'Task',
           component: () => import('@/views/task/index'),
           meta: { title: '任务控制', icon: 'tree' }
         }
       ]
     },
   
     {
       path: '/testqueue',
       component: Layout,
       children: [
         {
           path: 'index',
           name: 'testQueue',
           component: () => import('@/views/queue/index'),
           meta: { title: '测试队列', icon: 'form' }
         }
       ]
     },
     {
       path: '/taskmanager',
       component: Layout,
       meta: { title: '任务管理', icon: 'example' },
       children: [
         {
           path: 'list',
           name: 'taskList',
           component: () => import('@/views/taskList/index'),
           meta: { title: '任务列表', icon: 'form' }
         },
         {
           path: 'detail',
           name: 'taskDetail',
           component: () => import('@/views/taskDetail/index'),
           meta: { title: '任务清单', icon: 'form' }
         },
         {
           path: 'create',
           name: 'taskCreate',
           component: () => import('@/views/taskCreate/index'),
           meta: { title: '新建任务', icon: 'form' }
         },
       ]
     },
   	
     // 404 page must be placed at the end !!!
     { path: '*', redirect: '/404', hidden: true }
   ]
   ```

### 二.element-ui使用

#### 1.表格组件

```html
<el-table :data="list" border style="width: 100%;">
  <el-table-column prop="id" label="活动ID" min-width="1"></el-table-column>
  <el-table-column prop="name" label="活动名称" min-width="1"></el-table-column>
  <el-table-column prop="address" label="活动地址" min-width="1"></el-table-column>
  <el-table-column prop="starttime" label="开始日期" min-width="1"></el-table-column>
  <el-table-column label="操作" min-width="1">
    <template slot-scope="scope">
      <el-button type="text" size="small" @click="handleEdit(scope.row.id)">修改</el-button>
      <el-button type="text" size="small" @click="handleDelete(scope.row.id)">删除</el-button>
    </template>
  </el-table-column>
</el-table>

<script>
import gatheringApi from '@/api/gathering'
export default {
    data(){
      return {
          list:[]
      } 
    },
    created(){
        this.fetchData();
    },
    methods:{
        fetchData(){
           gatheringApi.getList().then(response => {
                this.list=response.data
           });
        }
    }
}
</script>
```

当没有使用template模板页时，一般加上prop属性，若有template时，可以为：

```html
<el-table-column label="主机名" min-width="2">
  <template slot-scope="scope">{{scope.row.name}}</template>
</el-table-column>
<el-table-column label="操作" min-width="3">
  <template slot-scope="scope">
    <el-button type="primary" size="small" @click="findTask(scope.$index,scope.row)">查看</el-button>
    <el-button type="info" size="small" :disabled="scope.row.status !== '空闲'" @click="createTask(scope.$index,scope.row)">新建</el-button>
  </template>
</el-table-column>
```

slot-scope用于指定当前行的上下文。使用scope.row可以获取行对象。

table组件的属性：

| 参数 | 说明       | 类型  |
| ---- | ---------- | ----- |
| data | 显示的数据 | array |

table-column组件的属性：

| 参数      | 说明               | 类型   |
| --------- | ------------------ | ------ |
| label     | 显示的标题         | string |
| prop      | 对应列内容的字段名 | string |
| width     | 对应列的宽度       | number |
| min-width | 按比例显示宽度     | number |

官方文档：[table组件](https://element.eleme.cn/#/zh-CN/component/table)

#### 2.分页组件

```html
<el‐table :data="list" border style="width: 100%"></el-table>
<el-pagination
  @size-change="fetchDate"
  @current-change="fetchDate"
  :current-page="currentPage"
  :page-sizes="[10,20,30,50]"
  :page-size="pageSize"
  layout="sizes, prev, pager, next, jumper, ->, total, slot"
  :total="total">
</el-pagination>

<script>
import gatheringApi from '@/api/gathering'
export default {
    data(){
      return {
         list:[],
         total:0,//总记录数
         currentPage:1,//当前页
         pageSize:10,//每页大小
         searchMap: {}//查询表单绑定变量
      } 
    },
    created(){
        this.fetchData();
    },
    methods:{
        fetchData(){
           gatheringApi.search(this.currentPage,this.pageSize,this.searchMap).then(response =>{
               this.list=response.data.rows
               this.total=response.data.total
           });
        }
    }
}
</script>
```

上面的分页方式是数据量比较大是传currentPage, pageSize给后端，由后端来实现分页；
数据量比较小时由前端实现分页，如下：

```html
<el-table border :data="list.slice((currentPage-1)*pageSize, currentPage*pageSize)" style="width: 100%;margin-top: 20px;"></el-table>
<el-pagination
  background
  @size-change="handleSizeChange"
  @current-change="handleCurrentChange"
  :current-page="currentPage"
  :page-size="pageSize"
  layout="sizes, prev, pager, next, jumper, ->, total"
  :total="list.length">
</el-pagination>

<script>
export default {
	data() {
		return {
			list: [],
			currentPage: 1,
			pageSize: 10,
		}
	},
	methods: {
			// 分页相关函数
	      handleSizeChange(val){
	        console.log(`每页${val}条`)
	        this.pageSize = val
	      },
	      handleCurrentChange(val){
	        console.log(`当前页：${val}`)
	        this.currentPage = val
	      },
	}
}
</script>
```

pagination的常用属性：

| 参数         | 说明                         | 类型     | 可选值                                     | 默认值                            |
| ------------ | ---------------------------- | -------- | ------------------------------------------ | --------------------------------- |
| page-size    | 每页显示条目个数             | number   | -                                          | 10                                |
| total        | 总条目数                     | number   | -                                          | -                                 |
| current-page | 当前页数，支持.sync修饰符    | number   | -                                          | 1                                 |
| layout       | 组件布局                     | string   | sizes,prev,pager,next,jumper,->,total,slot | prev,pager,next,jumper, ->, total |
| pages-sizes  | 每页显示个数选择器的选项设置 | number[] | -                                          | [10,20,30,40,50,100]              |

pagination的常用事件：

| 事件名称       | 说明                    | 回调参数· |
| -------------- | ----------------------- | --------- |
| size-changer   | pageSize改变时会触发    | 每页条数  |
| current-change | currentPage改变时会触发 | 当前页    |

官网链接：[pagination组件](https://element.eleme.cn/#/zh-CN/component/pagination)

#### 3.表单组件

在分页列表的基础上实现条件查询功能

```html
<!-- 行内表单：用于查询条件 -->
<el-form :inline="true">
  <el-form-item label="活动名称">
    <el-input v-model="searchMap.name" placeholder="活动名称"></el-input>
  </el-form-item>
  <el-form-item label="活动日期">
    <el-date-picker
      type="date"
      v-model="searchMap.startdate"
      placeholder="选择开始日期"
      ></el-date-picker>
    <el-date-picker
      type="date"
      v-model="searchMap.enddate"
      placeholder="选择结束日期"
      ></el-date-picker>
  </el-form-item>
  <el-button type="primary" @click="fetchDate">查询</el-button>
  <el-button type="primary" @click="handleEdit('')">新增</el-button>
</el-form>

<!-- 对话框表单  -->
<el-dialog title="活动编辑" :visible.sync="addDialogVisible">
	<el-form label-width="80px">
	  <el-form-item label="活动名称">
	    <el-input v-model="pojo.name" placeholder="活动名称"></el-input>
	  </el-form-item>
	  <el-form-item label="开始日期">
	    <el-date-picker type="date" v-model="pojo.starttime" placeholder="开始日期"></el-date-picker>
	  </el-form-item>
	  <el-form-item label="活动详情">
	    <el-input type="textarea" v-model="pojo.detail" :rows="2" placeholder="活动详情"></el-input>
	  </el-form-item>
	  <el-form-item label="是否可见">
	    <el-switch v-model="pojo.state" active-color="#13ce66" inactive-color="#ff4949"
	      active-value="1" inactive-value="0"></el-switch>
	  </el-form-item>
	  <el-form-item>
	    <el-button type="primary" @click="handerSave">保存</el-button>
	    <el-button @click="addDialogVisible=false">关闭</el-button>
	  </el-form-item>
	</el-form>
</el-dialog>
```

form（表单）组件官方文档：[form组件](https://element.eleme.cn/#/zh-CN/component/form)
input（文本框）组件官方文档：[input组件](https://element.eleme.cn/#/zh-CN/component/input)
date-picker（日期框）组件官方文档：[DatePicker组件](https://element.eleme.cn/#/zh-CN/component/date-picker)
switch（开关）组件官方文档：[switch组件](https://element.eleme.cn/#/zh-CN/component/switch)

注意：input组件中多行文本框设置

#### 4.对话框组件

```html
<el-dialog title="新建任务" :visible.sync="addDialogVisible" width="37%">
</el-dialog>

<script>
	data(){
		......
		addDialogVisible: false	// 对话框是否显示
	}
</script>
```

属性title为对话框标题，visible为是否显示。变量addDialogVisible用于控制对话框的显示，点击按钮将dialogFormVisible = true即可打开对话框。

dialog（对话框）组件官方文档：[dialog组件](https://element.eleme.cn/#/zh-CN/component/dialog)

#### 5.下拉选组件

```html
<el‐form‐item label="城市">
  <el‐select v‐model="pojo.city" placeholder="请选择">
    <el‐option
      v‐for="item in cityList"
      :key="item.id"
      :label="item.name"
      :value="item.id">
    </el‐option>
  </el‐select>
</el‐form‐item>

<script>
	import cityApi from '@/api/city'
	export default {
		data(){
			return {
				cityList: []
			}
		},
		created() {
	      cityApi.getList().then(response =>{
	        this.cityList = response.data
	      })
	    }
    }
</script>
```

select（下拉选）组件官方文档：[select组件](https://element.eleme.cn/#/zh-CN/component/select)

#### 6.消息提示组件

```js
this.$message({
    message: response.message,
    type: (response.flag?'success':'error')
});
// 或者
this.$message.success('请求成功！');
```

message（消息提示）组件官方文档：[message组件](https://element.eleme.cn/#/zh-CN/component/message)

#### 7.关于尺寸介绍

默认尺寸是最大的，其次中等、小、超小
[button按钮](https://element.eleme.cn/#/zh-CN/component/button)
size：medium / small / mini
[input输入框](https://element.eleme.cn/#/zh-CN/component/input)
size：medium / small / mini
[select选择器](https://element.eleme.cn/#/zh-CN/component/select)
size：medium / small / mini
[form表单](https://element.eleme.cn/#/zh-CN/component/form)
label-width：表单域标签的宽度
size：medium / small / mini （用于控制该表单域下组件的尺寸）
[table表格](https://element.eleme.cn/#/zh-CN/component/table)
size：medium / small / mini

### 三.vue中使用element-ui示例

对活动管理的查询、分页、条件查询，增加，修改，删除等。采用的easy-mock的模拟接口。

#### gathering.vue

```html
<template>
  <div>
    <br/>
    <el-form :inline="true">
      <el-form-item label="活动名称">
        <el-input v-model="searchMap.name" placeholder="活动名称"></el-input>
      </el-form-item>
      <el-form-item label="活动日期">
        <el-date-picker
          type="date"
          v-model="searchMap.startdate"
          placeholder="选择开始日期"
          ></el-date-picker>
        <el-date-picker
          type="date"
          v-model="searchMap.enddate"
          placeholder="选择结束日期"
          ></el-date-picker>
      </el-form-item>
      <el-button type="primary" @click="fetchDate">查询</el-button>
      <el-button type="primary" @click="handleEdit('')">新增</el-button>
    </el-form>

    <el-table :data="list" border style="width: 100%;">
      <el-table-column prop="id" label="活动ID" min-width="1"></el-table-column>
      <el-table-column prop="name" label="活动名称" min-width="1"></el-table-column>
      <el-table-column prop="address" label="活动地址" min-width="1"></el-table-column>
      <el-table-column prop="starttime" label="开始日期" min-width="1"></el-table-column>
      <el-table-column label="操作" min-width="1">
        <template slot-scope="scope">
          <el-button type="text" size="small" @click="handleEdit(scope.row.id)">修改</el-button>
          <el-button type="text" size="small" @click="handleDelete(scope.row.id)">删除</el-button>
        </template>
      </el-table-column>
    </el-table>

    <el-pagination
      @size-change="fetchDate"
      @current-change="fetchDate"
      :current-page="currentPage"
      :page-sizes="[10,20,30,50]"
      :page-size="pageSize"
      layout="sizes, prev, pager, next, jumper, ->, total, slot"
      :total="total">
    </el-pagination>

    <el-dialog title="活动编辑" :visible.sync="addDialogVisible">
      <el-form label-width="80px">
        <el-form-item label="活动名称">
          <el-input v-model="pojo.name" placeholder="活动名称"></el-input>
        </el-form-item>
        <el-form-item label="开始日期">
          <el-date-picker type="date" v-model="pojo.starttime" placeholder="开始日期"></el-date-picker>
        </el-form-item>
        <el-form-item label="活动详情">
          <el-input type="textarea" v-model="pojo.detail" :rows="2" placeholder="活动详情"></el-input>
        </el-form-item>
        <el-form-item label="选择城市">
          <el-select v-model="pojo.city" placeholder="请选择">
            <el-option
              v-for="item in cityList"
              :key="item.id"
              :value="item.id"
              :label="item.name"></el-option>
          </el-select>
        </el-form-item>
        <el-form-item label="是否可见">
          <el-switch v-model="pojo.state" active-color="#13ce66" inactive-color="#ff4949"
            active-value="1" inactive-value="0"></el-switch>
        </el-form-item>
        <el-form-item>
          <el-button type="primary" @click="handerSave">保存</el-button>
          <el-button @click="addDialogVisible=false">关闭</el-button>
        </el-form-item>
      </el-form>
    </el-dialog>

  </div>
</template>

<script>
  import gatheringApi from '@/api/gathering'
  import cityApi from '@/api/city'

  export default {
    data(){
      return {
        list: [], // 当前活动列表
        // 分页初始化
        currentPage: 1, // 当前页
        pageSize: 20, // 每页条数,默认是10
        searchMap: {}, //查询条件
        total: 0, // 总条数
        // 对话框
        addDialogVisible: false,
        pojo: {},  // 对话框表单数据对象
        // 城市下拉选列表
        cityList: [],  //城市列表
        id: '', // 当前编辑的id
      }
    },
    created() {
      this.fetchDate();
      cityApi.getList().then(res => {
        this.cityList = res.data;
        console.log(res.data)
      })

    },
    methods: {
      // 分页根据条件获取列表数据
      fetchDate(){
        gatheringApi.search(this.currentPage, this.pageSize, this.searchMap).then(res => {
          this.list = res.data.rows;    // 当前页面列表
          this.total = res.data.total;  // 总条数
          console.log(res.data.rows)
        }).catch(err => {
          alert('请求失败!')
        })
      },
      // 新增或修改
      handleEdit(id){
        this.id = id; // 标准是新增还是修改
        this.addDialogVisible = true; // 打开窗口
        if (id){  // 修改
          // 调用查询
          gatheringApi.findById(id).then(response => {
            this.pojo = response.data;
          })
        } else {  // 新增
          this.pojo = {}  // 清空表单
        }
      },
      // 保存
      handerSave(){
        gatheringApi.updateById(this.id,this.pojo).then(res => {
          this.$message({
            message: res.message,
            type: (res.flag? 'success':'error'),
          });
          if (res.flag){  //添加成功
            this.fetchDate();
          }
        });
        this.addDialogVisible = false;  //关闭对话框
      },
      handleDelete(id){
        this.$confirm('确定要删除此条记录吗','提示',{
          confirmButtonText: '确定',
          cancelButtonText: '取消',
          type: 'warning'
        }).then(() => {
          gatheringApi.deleteById(id).then(res => {
            this.$message({
              message: res.message,
              type: (res.flag? 'success':'error')
            });
            if (res.flag){
              this.fetchDate(); // 刷新数据
            }
          });
        }).catch(() => {});
      }

    },

  }
</script>

<style>

</style>
```

#### gathering.js

```js
import request from '@/utils/request'
const group_name = 'gathering'
const api_name = 'gathering'

export default {
  getList() { // 获取所有
    return request(
      {
        url: `/${group_name}/${api_name}`,
        method: 'get'
      }
    );
  },
  search(page, size, searchMap){  // 分页
    return request(
      {
        url: `/${group_name}/${api_name}/search/${page}/${size}`,
        method: 'post',
        data: searchMap
      }
    );
  },
  save(pojo){   // 新增对话框保存
    return request(
      {
        url: `/${group_name}/${api_name}`,
        method: 'post',
        data: pojo
      }
    )
  },
  findById(id){ // 根据id查询
    return request({
      url: `/${group_name}/${api_name}/${id}`,
      method: 'get'
    });
  },
  updateById(id,pojo){
    if (id == null || id == ''){
      return this.save();
    };
    return request({
      url: `/${group_name}/${api_name}/${id}`,
      method: 'put',
      data: pojo
    });
  },
  deleteById(id){
    return request({
      url: `/${group_name}/${api_name}/${id}`,
      method: 'delete'
    });
  }
}
```

#### city.js

```js
import request from "@/utils/request"
export default {
  getList(){
    return request(
      {
        url:'/base/city',
        method:'get'
      }
    );
  }
}
```