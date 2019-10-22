---
title: "vue 指令事件"
date: 2019-09-24T16:26:04+08:00
draft: false
---

### 过滤器
Vue.js支持在 {{}} 插值的尾部添加一小管道符 "|" 对数据进行过滤，经常用于格式化文本，比如字母全部大写、货币千位使用逗号分隔等。过滤的规则是自定义的,通过给 Vue 实例添加选项 filters 来设置过滤器：
`{{ data | filter1 |filter2}}`
`{{date | formatDate(66,99)}}`中的第一个和第二个参数，分别对应过滤器的第二个和第三个参数

### 指令和事件
指令（Directives）是 Vue 模板中最常用的一项功能，它带有前缀 v-，能帮我们快速完成DOM操作。循环渲染。显示和隐藏。

* v-­text:解析文本 和{{ }} 作用一样

* v­-html:解析html

* v-­bind: v­bind 的基本用途是动态更新 HTML 元素上的属性，比如 id、class 等

* v-­on: 它用来绑定事件监听器
### v­-on具体介绍
1. 在普通元素上，v-­on可以监听原生的 DOM 事件，除了 click 外，还有dblclick、 keyup, mousemove 等。表达式可以是一个方法名，这些方法都写在 Vue 实例的 methods 属性内，并且是函数的形式，函数内的 this 指向的是当前 Vue 实例本身，因此可以直接使用 this.xxx形式来访问或修改数据
2. vue中用到的所有方法都定义在methods中

### 语法糖
* 语法糖是指在不影响功能的情况下，添加某种简洁方法实现同样的效果，从而更加方便程序开发。
v-bind ——> : (冒号)
v-on ——> @

### 实例
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
        <!-- 过滤器 符号 | ... -->
        {{date | formatDate}} 
        <!-- formatDate('arg1','arg2'),arg1,arg2 与 下面filter中的formatDate(value,arg1,arg2)对应 -->
    <hr> 
    指令和事件, v- <br>
    v-text指令:解析文本<br>
    {{apple}}<br>
    <span v-text="apple"></span>
    <hr>
    v-html指令:解析html<br>
    {{banana}}<br>
    <span v-text="banana"></span><br>
    <span v-html="banana"></span><br>
    <hr>
    v-bind: 绑定活的属性<br>
    <div v-bind:class="className"></div><br>
    <hr>
    v-on: 绑定事件监听器 <br>
    <button v-on:click="count">{{countnum}}</button>
    <hr>
    语法糖：在不影响功能的情况下，添加某种简洁方法实现同样的效果
    <br>
    v-bind:  ===>  :<br>
     v-on:    ===>  @
</div>
    <script src="https://cdn.jsdelivr.net/npm/vue@2.5.16/dist/vue.js"></script>
    <script>
    // 需求：在页面中实时显示当前时间
    // 在年月日时分秒为个位数时前面自动补零
    var plusDate = function(value){
        return value<10 ? '0'+value : value;
    }

    var app = new Vue({
        el: "#dateApp",
        data:{
            date: new Date(),
            apple: 'xxxx',
            banana:'<span style="color: yellow;">香蕉</span>',
            className: 'transRed',
            countnum:0
        },
        //定义过滤器
        filters:{
            formatDate: function(value){
                //将字符串转化为 date 类型，value为要过滤的数据
                var date = new Date(value);
                var year = date.getFullYear();
                var month = plusDate(date.getMonth()+1);
                var day = plusDate(date.getDate());
                var hours = plusDate(date.getHours());
                var min = plusDate(date.getMinutes());
                var sec = plusDate(date.getSeconds());
            //将整理好的数据返回
            return year +'.'+ month +'.'+day +'   '+ hours+'-'+min+'-'+sec+'.';
            }
        },
        methods:{
            count: function(){
            this.countnum= this.countnum +1;
        }
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
    <style>
    .transRed{
        background-color:red;
        height: 20px;
    }
    </style>
</body>
</html>
```