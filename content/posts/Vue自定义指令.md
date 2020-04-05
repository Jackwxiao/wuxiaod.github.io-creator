---
title: "vue自定义指令"
date: 2019-09-16T20:30:30+09:00
draft: false
---
## 自定义指令
#### 自定义指令的基本用法
和组件类似分全局注册和局部注册，区别就是把component换成了derective
```
需求：自动获取焦点
    <div id="app">
        获取焦点：<input type="text" v-focus><br>
        未加自定义指令v-focus：<br>
        <input type="text">
    </div>
<script src="https://cdn.jsdelivr.net/npm/vue@2.5.16/dist/vue.js"></script>
    <script>
    Vue.directive('focus',{
        inserted:function(el){
            //el：指令所绑定的元素可以用来直接操作DOM
            el.focus();
        }
    });
     var app = new Vue({
        el:'#app',
        data:{

        }
    })
    </script
```
#### 钩子函数
指令定义函数提供了几个钩子函数（可选）：

* bind: 只调用一次，指令第一次绑定到元素时调用，用这个钩子函数可以定义一个在绑定时执行一次的初始化动作。
  
* inserted: 被绑定元素插入父节点时调用（父节点存在即可调用，不必存在于 document中）

* update: 被绑定元素所在的模板更新时调用，而不论绑定值是否变化。通过比较更新前后的绑定值，可以忽略不必要的模板更新（详细的钩子函数参数见下）。
  
* componentUpdated: 被绑定元素所在模板完成一次更新周期时调用。

* unbind: 只调用一次， 指令与元素解绑时调用。

#### 钩子函数的参数有：
* el: 指令所绑定的元素，可以用来直接操作 DOM 。

* binding: 一个对象，包含以下属性：
——–name: 指令名，不包括 v­ 前缀。
——–value: 指令的绑定值， 例如： v­my­directive=”1 + 1”, value 的值是 2。
——–oldValue: 指令绑定的前一个值，仅在 update 和 componentUpdated 钩子中可用。无
论值是否改变都可用。
——–expression: 绑定值的字符串形式。 例如 v­my­directive=”1 + 1” ， expression 的值是
“1 + 1”。
——–arg: 传给指令的参数。例如 v­my­directive:foo， arg 的值是 “foo”。
——–modifiers: 一个包含修饰符的对象。 例如： v­my­directive.foo.bar, 修饰符对
象。modifiers 的值是 { foo: true, bar: true }。

* vnode: Vue 编译生成的虚拟节点。

* oldVnode: 上一个虚拟节点，仅在 update 和 componentUpdated 钩子中可用
```
    <div>
自定义指令钩子函数得参数：<br>
        <div v-cuihua:goudan.a.b.c="obq"></div>
    </div>
  <script src="https://cdn.jsdelivr.net/npm/vue@2.5.16/dist/vue.js"></script>
    <script>
    <script>
       Vue.directive('cuihua',{
       bind:function(el,binding,vnode){
           var keys=[];
           for(var key in vnode){
               keys.push(key)
           }
        el.innerHTML = 
        'name' + ':' + binding.name+'<br>'+
        'value' + ':' + binding.value+'<br>'+
        'expression' + ':' + binding.expression+'<br>'+
        'argument' + ':' + binding.arg+'<br>'+
        'modifiers' + ':' + JSON.stringify(binding.modifiers)+'<br>'+
        'vnode中的keys' + ':' + keys.join('--')+'<br>'
        
       }
    });
    var app = new Vue({
        el:'#app',
        data:{
            abcde:'我是value值',
            obq:'我是自定义指令绑定的值'
        }
    })
    </script>