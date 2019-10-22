---
title: "vue组件详解"
date: 2019-10-08T15:30:30+08:00
draft: false
---
## 组件（一）
#### 一、组件的作用
组件的作用：提高代码的复用性。
#### 二、组件的使用方法
1.全局注册组件：
```
全局注册：
<html>
<my-component></my-component>
</html>

<script>
Vue.component('my-component',{
    template:'<div>这是全局注册的组件</div>'
})
</script>
```
2.局部注册组件：
```
<html>
<app-component></app-component>
</html>
在vue实例中定义组件
<script>
var app = new Vue({
    el:'#app',
    components:{
        'app-component':{
            template:'<div>这是局部注册的组件</div>'
        }
    }
})
</script>
```
**两种注册方式的优缺点：**
全局注册：优点是所有的vue实例都可以使用；缺点是权限太大，容错率低。
局部注册：只能在所注册的vue实例中使用。
<br>
**注意事项：**
1. html标签受限：Vue组件模板在某些情况下回受到html标签的限制，如table标签内只能有tr、td、tbody这些元素,如果把组件名挂载到table内,实际内容是不会被渲染到table标签中的，会把组件内容踢出去。
此时需要 is 属性来挂载组件名，`is='组件名'`
```
<table>
   <tbody is = 'app-component'></tbody>
</table>
```
2. 组件名使用小写字母或小写字母加 -  来命名；
3. template中的内容必须被一个DOM元素包裹，可以嵌套；
4. 在组件定义中，除了template之外的其他选项:data,omputed,methods
5. 组件中，data必须是一个方法。

#### 三、使用props传递数据
在子组件中使用props接收来自父组件的数据，在props中定义的属性，都可以在组件中直接使用；
子组件中的props来自父组件，子组件中的data return的是组件自己的数据；props中定义的和data返回的，其作用域都是组件本身，可以在template,methods,computed中直接使用。

```
<div id="app" style="border:1px solid red;height: 350px;">
        <h5 style="text-align: center">父组件</h5>
        <child-component msg="这是来自父组件的内容"></child-component>
        <!-- msg是父组件传过来的 -->

        <input type="text" v-model="parentmsg">
        <bind-component v-bind:msg="parentmsg"></bind-component>
    </div>

    <script src="https://cdn.jsdelivr.net/npm/vue@2.5.16/dist/vue.js"></script>
    <script>
    var app = new Vue({
        el:'#app',
        data:{
            parentmsg:'parentmsg'
        },
        components:{
            'child-component':{
                props:['msg'],//子组件使用父组件的数据信息，用props，props的内容来自父组件
                template:'<div style="border:1px solid blue;height: 75px;">{{msg}}</div>',
            },
            'bind-component':{
                props:['msg'],
                template:'<div style="border:1px solid yellow;height: 75px;">{{msg}}</div>',
            }
        }
    })
    </script>
```
使用v-bind进行数据的动态绑定；把input中的msg传递给子组件，input v-model中绑定的是父组件中data的parentmsg,要传递给子组件需用v-bind绑定属性再用props接收。
#### 四、单项数据流，props的应用场景
1. 单向数据流：通过props传递数据是单向的，也就是父组件数据变化时会传递给子组件，但反过来不行。
2. 目的：尽可能的将父子组件解耦，避免子组件无意中修改父组件的状态。
3. 两种应用场景：<br>
* 父组件传递初始值进来，子组件将它作为初始值保存起来，在自己的作用域下可以随意使用和修改；
`这种情况可以在组件data 中再声明一个数据，引用父组件的prop。`
步骤：
a: 注册组件；
b: 将父组件的数据传递进来，并在子组件中用props接收；
c: 将传递进来的数据通过初始值保存起来。
* prop作为需要被转变的原始值传入，使用计算属性重新计算。
`通过input输入动态改变width，将传递进来的数据通过计算属性重新计算。`
```
<div id="app">
        <my-component msg="应用一:我是父组件传递的数据"></my-component>
        <hr>
    应用二: <input type="text" v-model="width">
    <width-component :width="width"></width-component>
    </div>

    <script src="https://cdn.jsdelivr.net/npm/vue@2.5.16/dist/vue.js"></script>
    <script>
    Vue.component('my-component',{
         props:['msg'],
         template:'<div>{{count}}</div>',
         data:function(){
             return{//props中的值可以通过this.xxx来进行获取
                 count:this.msg
             }
         }
    })
    Vue.component('width-component',{
         props:['width'],
         template:'<div :style="style"></div>',
         data:function(){
             return{}
             },
        computed:{
            style:function(){
                return{//重新计算
                    width:this.width + 'px',
                    background:'red',
                    height:'30px'
                }
            }
        }
         })
      var app = new Vue({
          el:'#app',
          data:{
              width:0
          },
          
      })
     </script>
```
#### 五、组件通信
##### 子组件给父组件传递数据--自定义事件
使用v­on 除了监昕 DOM 事件外，还可以用于组件之间的自定义事件。JavaScript 的设计模式 一一观察者模式， dispatchEvent 和addEventListener这两个方法。Vue 组件也有与之类似的一套模式，子组件使用 \$emit() 来触发事件，父组件使用 \$on() 来监听子组件事件。
第一步：自定义事件change；
第二步：在子组件的methods中用 \$emit()触发事件，第一个参数是事件名change，后边的参数是要传递的数据this.count；
第三步：在自定义事件中用一个参数value来接受传过来的数据。
```
//通过点击子组件的按钮实现父组件余额total的加减
<div id="app">
    <h5>子组件给父组件传数据<h5>
    余额：{{total}}
    <son-component @change="handletotal"></son-component>
</div>

 <script>
    Vue.component('son-component',{
        template:'<div>\
              <button @click="handleincrease">+1000</button>\
              <button @click="handlereduce">-1000</button>\
        </div>',
        data:function(){
            return {
                count:1000
            }
        },
        methods:{
            handleincrease:function(){
                this.count=this.count +1000;
                this.$emit('change',this.count)
                //change是emit 触发的事件名，后面的参数是要传递的数据
            },
            handlereduce:function(){
                this.count= this.count -1000;
                this.$emit('change',this.count)
            }
        }
    })
    var app = new Vue({
        el:'#app',
        data:{
          total:1000
        },
        methods:{
            handletotal:function(value){
            //此处的形参value 是传过来的数据this.count
                   this.total=value;
            }
        }
    })
    </script>
```
##### 在组件中使用v-model
$emit的代码,这行代码实际上会触发一个 input事件, ‘input’后的参数就是传递给v-­model绑定的属性的值。
**v-­model 其实是一个语法糖，这背后其实做了两个操作:**
* v­-bind 绑定一个value 属性;
* v-­on 指令给当前元素绑定 input 事件。

**要使用v­-model,要做到:**
* 接收一个 value 属性。
* 在有新的 value 时触发 input 事件。

```
<div id="app">
        余额为：{{total}}
        <son-component v-model="total"></son-component>   
    <!-- v-model其实就绑定了input事件，当触发input时，input事件会自动接受传递过来的参数，并赋值给已绑定的total-->
    </div>
    <script src="https://cdn.jsdelivr.net/npm/vue@2.5.16/dist/vue.js"></script>
    <script>
    Vue.component('son-component',{
        template:'<div>\
              <button @click="handleincrease">+1000</button>\
        </div>',
        data:function(){
            return {
                count:1000
            }
        },
        methods:{
            handleincrease:function(){
                this.count=this.count +1000;
                this.$emit('input',this.count)
            },
        }
    })
    var app = new Vue({
        el:'#app',
        data:{
          total:1000
        },
        // methods:{
        //     handletotal:function(value){//此处的形参value 是传过来的数据this.count
        //            this.total=value;
        //     }
        // }
    })
    </script>
```
#### 六、非父组件之间的通信
有时候两个组件（非父子关系）也需要通信。在简单情况下，可以用一个空的Vue实例作为中央事件总线。
`vue bus = new Vue()`
`bus.$emit('is-selected',1)`
`bus.$on('id-selected',function(id){.....})`
```
<div id="app">
        <my-acomponent ref='a'></my-acomponent>
        <my-bcomponent ref='b'></my-bcomponent>
    </div>
    <script src="https://cdn.jsdelivr.net/npm/vue@2.5.16/dist/vue.js"></script>
<script>
    Vue.component('my-acomponent',{
    template:'<div><button @click="handle">点击我向B组件传递数据</button></div>',
    data:function () {
    return{
    aaa:'我是来自A组件的内容'
    }
    },
    methods:{
        handle:function () {
            this.$root.bus.$emit('lala',this.aaa);
            }
        }
    })
    Vue.component('my-bcomponent',{
        template:'<div></div>',
        created:function () {
    //A组件在实例创建的时候就监听事件---lala事件
            this.$root.bus.$on('lala',function (value) {
            alert(value)
        });
    }
    })
</script>
```
父链：this.$parent
```
    Vue.component('child-component',{
        template:'<button @click="setFatherData">通过点击我修改父亲的数据</button>',
        methods:{
            setFatherData:function () {
                this.$parent.msg = '数据已经修改了'
                }
            }
        })
```
子链：this.$refs

提供了为子组件提供索引的方法，用特殊的属性ref为其增加一个索引
```
    var app = new Vue({
        el:'#app',
        data:{//bus中介
            bus:new Vue(),
            msg:'数据还未修改',
            formchild:'还未拿到'
        },
            methods:{
            getChildData:function () {
        //用来拿子组件中的内容 ----$refs
            this.formchild = this.$refs.c.msg;
            }
        }
    })
```
