---
title: "vue-router、axios、vuex"
date: 2019-09-17T20:30:30+09:00
draft: false
---
## vue-router
#### 路由的基本加载
1. 安装
`npm install --save vue-router`
2.引用 
```
在router 的 index.js 中 import router from 'vue-router'
Vue.use(router)
```
3. 配置路由文件，并在vue实例中注入
```
var rt = new router({ 
routes:[{ 
    path:'/', //指定要跳转的路径 
    component: HelloWorld //指定要跳转的组件
    }] 
}) 
new Vue({ 
el: '#app',
router, 
components: { App }, 
template: '' 
})
```
4. 确定试图加载的位置
```
<router-view></router-view>
```
#### 路由的跳转
```

<template>
    <ul>
        <li> 
           <router-link to="/helloearth">HELLO EARTH 33</router-link>
        </li>
        <li>
            <router-link to="/helloworld">HELLO WORLD 22</router-link>
        </li>
    </ul>
</template>

```
#### 路由参数传递

* 必须在路由内加入路由的name 
* .必须在path后加/: +传递的参数

传递参数：
<router-link :to="{name：helloearth,params:{msg: xxxxx}}">
HELLO WORLD
</router-link>
读取参数：
 $route.params.msg
 
## Axios
#### 简介
Axios是一个基于Promise用于浏览器和nodejs的HTTP客户端，它本身具有以下特征：
* 从浏览器中创建 XMLHttpRequest 
* 从node.js 发出http请求
* 支持Promise API
* 拦截请求和响应
* 转换请求和响应数据
* 取消请求
* 自动转换JSON数据
* 客户端支持防止 CSRF/XSRF

#### get请求：
1. 安装 
```
npm install axios
``` 
2. 引入加载  
```
import axios from 'axios' 
```
3. 将axios全局挂载到VUE原型上 
```
Vue.prototype.$http = axios;
```

4. 发出请求 以cnode社区API为例子
```

<template>
  <div class="hello">
   <h3>这是axios项目，用来发送请求，拦截响应</h3>
   <button @click='getData'>GET方式发送请求</button>
   <ul>
     <li v-for="item in items">
       {{item.title}}
     </li>
   </ul>
  </div>
</template>
<script>
Vue.prototype.$http = axios  //将axios 全局挂载到Vue原型上
import axios from 'axios' //引入加载
import Vue from 'vue'

export default {
  name: 'HelloWorld',
  data () {
    return {
      items:[]
    }
  },
  methods:{
    getData(){//请求以cnode社区API为例子
      this.$http.get('https://cnodejs.org/api/v1/topics',{
        params:{//这是参数
          page: 2,
          limit:5
        }
      })
      .then((res)=>{//此处用了ES6语法，不用箭头函数下面使用this.items 会出错
        this.items = res.data.data
        console.log(this.items)
      })
      .catch((err)=>{
        console.log(err)
      })
    }
  }
</script>
<style scoped>
</style>
```

参数：page页码
limit 每页显示的数量

#### post请求：


POST传递数据有两种格式： 
* form-data ？page=1&limit=48 

* x-www-form-urlencoded { page: 1,limit: 10 }
 
在axios中，post请求接收的参数必须是form-data
qs插件—qs.stringify


```
<template>
  <div class="hello">
   <h3>这是axios项目，用来发送请求，拦截响应</h3>
   <button @click='postData'>post方式发送请求</button>
   <ul>
     <li v-for="item in items">
       {{item.title}}
     </li>
   </ul>
  </div>
</template>

<script>

Vue.prototype.$http = axios  //将axios 全局挂载到Vue原型上
import Vue from 'vue'
import qs from 'qs'

export default {
  name: 'HelloWorld',
  data () {
    return {
      items:[]
    }
  },
  methods:{
  postData(){
      this.$http.post(url,qs.stringify({
        params:{//这里的参数必须经过qs插件使用stringify使其格式为 form-data 
        //因为在axios中，post请求接收的参数必须是 form-data
          page: 2,
          limit:5
        }
      }))
      .then((res)=>{
        this.items = res.data.data
        console.log(this.items)
      })
      .catch((err)=>{
        console.log(err)
      })
    }
}
</script>

<style scoped>

</style>
```
## Vuex 
用来管理状态，共享数据，在各个组件之间管理外部状态 
比如说，用于在每个页面（组件）都要判断用户是否是登录状态，如果没登陆则要求登录。
创建一个状态仓库，每个组件都能共享这个状态仓库。

1. 父组件给子组件传递数据，首先父组件的data属性中必须得有这个数据（:msg="toSonMsg"），其次子组件中必须得有一个props来接受数据(msg:{
type:String, default : ' ' })，然后在页面渲染就行了（{{msg}}）

2. 子组件给父组件传递数据，在父组件模板下面的渲染的子组件中先定义一个自定义事件

```
<son @handle="getSonMsg"></son> 
```

子组件模板中定义一个

```
<button @click="senMsgFa"></button>
```
在子组件还要定义一个senMsgFa方法
```
methods:{
   senMsgFa:function(){
   this.$emit('handle',要传给父组件的数据)
   }
}
```
之后在子组件的data属性中定义要传给父组件的数据toFaMsg
```
data:function(){
    return{
      toFaMsg:'这是子组件传给父组件的'
    }
  }
```
然后上面$emit中的第二个参数就是this.toFaMsg，也就是
```
this.$emit('handle',this.toFaMsg)
```
点击按钮触发handle事件之后就会把toFaMsg传递出去，父组件中getSonMsg方法中的参数value就是传进来的toFaMsg，这时候只要把value赋值给this.fromSonMsg,然后渲染出来就行了{{fromSonMsg}}

**由上可知，子父组件之间相互可以随意传递数据，如果有”第三者“组件的诞生呢，虽然可以让第三者也成为子父组件关系，但如果组件数量逐渐增多，这种办法就不现实了，于是，用Vuex吧。**
#### 使用Vuex需要安装vuex插件
```
npm install vuex
```
#### 引入vuex，并Vue.use（Vuex）
```
//在main.js中
import Vuex from 'vuex'
Vue.use(Vuex)
```
#### 创建状态仓库：Store、state不能改
```
//在main.js 中
var store = new Vuex.Store({
    state:{
        num: 99
    }
})
//然后在main.js中注入store（和router类似）
```
#### 共享数据
如果每个组件都需要这个状态：那就每个组件都设置一个计算属性
 ```
 computed:{
   getNum:function(){
    return this.$store.state.num
    }
 }
```
 然后再渲染出来{{getNum}}

#### 操作状态
main.js
 ```
//创建状态仓库
var store = new Vuex.Store({
    state:{
      num: 99
    },
    mutations:{//定义状态改变函数
      increase(state){//ES6写法
        state.num=state.num+20
      },
      decrease:function(state){
        state.num=state.num-20
      }
    },
    actions:{
       //context 上下文对象
      decreaseActions:function(context){
        //actions中只能对mutation进行操作
        context.commit('decrease')
      }
    },
    getters:{
      getNum(state){
        return state.num > 0 ? state.num : 0
      }
    }
})
```

* 各组件通过this.$store.commit('increase')来直接操作state ------mutation
* 通过this.$store.dispatch('decrease')来间接操作state ------actions
 
**mutation与actios的区别：**
1. 参数不同：
mutation参数为state，actions参数为context
2. 调用方式不同：
前者通过：`this.$store.commit()`
后者通过：`this.$store.dispatch()`
3. 函数要求不同：
actions中可以使用异步操作，mutation中只能同步

vuex状态管理操作的流程：
view->actions->mutation->state->view
对state进行直接操作的是mutation，actions如果没有异步操作可以省略。

**后面优化时再单独创建store>index.js文件，再把vuex相关的代码放到里面，类似vue-router**

------


(笔记链接:))[https://app.yinxiang.com/fx/ecad8f59-6bcc-4d70-a722-6242379ef32c]