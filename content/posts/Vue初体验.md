---
title: "vue基础之实例与数据绑定"
date: 2019-09-14T15:30:30+08:00
draft: false
---
# vue实例和数据绑定
1. cdn引入vue.js：`<script src="https://cdn.jsdelivr.net/npm/vue@2.5.16/dist/vue.js"></script>`
* 通过构造函数 Vue 就可以创建一个 Vue 的根实例，并启动 Vue 应用---入口
```
   var app ＝new Vue({
     el:'',
     data:{
   }
   })
```
2. 必不可少的一个选项就是 el 。 el 用于指定一个页面中己存在的 DOM 元素来挂载 Vue实例,可以是标签。也可以是css语法。
3. 通过 Vue 实例的 data 选项，可以声明应用内需要双向绑定的数据。建议所有会用到的数据都预先在 data 内 声明，这样不至于将数据散落在业务逻辑中，难以维护。也可以指向一个已经有的变量。
4. 挂载成功后，我们可以通过
    `app.$el`
   来访问该元素。Vue 提供了很多常用的实例属性与方法，都以
   el,app.
   ---访问data元素的属性--- 直接app.属性名，`app.msg`

## 生命周期钩子
created:
* 实例创建完成后调用，此阶段完成了数据的观测等，但尚未挂载， $el 还不可用；
* 需要初始化处理一些数据时会比较有用.----还未挂载；
mounted el:
* 挂载到实例上后调用，一般我们的第一个业务逻辑会在这里开始；
* 相当于 $(document).ready()---刚刚挂载；
beforeDestroy:
* 实例销毁之前调用；
* 主要解绑一些使用 addEventListener 监听的事件等。
## 文本插值和表达式
* 语法：使用双大括号(Mustache 语法）`{{}}`是最基本的文本插值方法，它会自动将双向绑定的数据实时显示出来。
* 用法: 在`{{}}`中，除了简单的绑定属性值外，还可以使用 JavaScript 表达式进行简单的运算、三元运算等。
<br>
Vue .js 只支持单个表达式，不支持语句和流控制。
`{{6+6 *3}}    //可以进行简单的运算`
`{{6<3?msg:a}} //可以用三元运算符`
`{{if(6>3){}} //注意：文本插值的形式，其中不能书写表达式,支持单个表达式。`
el，Vue实例本身也代理了data对象里的所有属性，所以可以这样访问:
访问vue实例的属性: `app.data`

```
{{var a = 6}}
var a ;a = 6;     //这是语旬，不是表达式
{{var book ='Vue.js 实战'}}  //不能使用流控制，要使用三元运算`
{{if (ok) return msg}}
```
## 实例
```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>demo</title>
</head>
<body>
    <div id="dateApp">
        {{date}}
    </div>
    <script src="https://cdn.jsdelivr.net/npm/vue@2.5.16/dist/vue.js"></script>
    <script>
    /*  需求：在页面中实时显示当前时间*/
    var app = new Vue({
        el: "#dateApp",
        data:{
            date: new Date()
        },
        mounted: function(){
            var _this= this;//this 代表 vue 实例
            this.timer = setInterval(function(){
                _this.date= new Date();
            },1000)
        },
        beforeDestory:function(){
            if(this.timer){
                clearInterval(this.timer)
            }
        }


    })
    </script>
</body>
</html>
```