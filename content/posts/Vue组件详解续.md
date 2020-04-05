---
title: "vue组件详解二"
date: 2019-09-15T16:30:30+08:00
draft: false
---
## 组件（二）
#### 七、插槽slot
1. 插槽：为了让组件可以组合，我们需要一种方式来混合父组件的内容与子组件自己的模板。这个过程被称为 内容分发。Vue.js 实现了一个内容分发 API，使用特殊的 ‘slot’ 元素作为原始内容的插槽。
2. 编译的作用域：假定模板为：
```
    <child-component>
        {{ message }}
    </child-component>
```
message 应该绑定到父组件的数据，还是绑定到子组件的数据？答案是父组件。组件作用域简单地说是：
    父组件模板的内容在父组件作用域内编译；
    子组件模板的内容在子组件作用域内编译。
插槽的用法：父组件的内容与子组件相混合，从而弥补了视图的不足。
**混合父组件的内容与子组件自己的模板**
3. 单个插槽：
```
    <div id="app" >
        <my-component>
            <p>我是父组件的内容</p>
        </my-component>
    </div>
    Vue.component('my-component',{
        template:'<div>\
            <slot>\
            如果父组件没有插入内容，我就作为默认出现\   
            </slot>\
    </div>'
    })
```
4. 具名插槽：
```
    <name-component>
        <h3 slot="header">我是标题</h3>
        <p>我是正文内容</p>
        <p>正文内容有两段</p>
        <p slot="footer">我是底部信息</p>
    </name-component>
    Vue.component('name-component',{
        template:'<div>\
            <div class="header">\n' +
            '<slot name="header">\n' +
            ' \n' +
            ' </slot>\n' +
            '</div>\n' +
            '<div class="contatiner">\n' +
            ' <slot>\n' +
            ' \n' +
            ' </slot>\n' +
            '</div>\n' +
            '<div class="footer">\n' +
            ' <slot name="footer">\n' +
            '\n' +
            ' </slot> \n' +
            '</div>'+
            ' </div>'
    })
```
5. 作用域插槽：作用域插槽是一种特殊的slot，使用一个可以复用的模板来替换已经渲染的元素——从子组件获取数据===template模板是不会被渲染的
```
    Vue.component('my-component',{
        template:'<div>\
        <slot text="我是来自子组件的数据" ss="fdjkfjlsd" name="abc">\
        </slot>\
        </div>'
    })
```
6. 访问slot:通过this.$slots.(name)
```
    mounted:function () {
    //访问插槽
    var header = this.$slots.header;
    var text = header[0].elm.innerText;
    var html = header[0].elm.innerHTML;
    console.log(header)
    console.log(text)
    console.log(html)
    }
```
#### 八、动态组件
vue给我们提供了一个component元素，用来动态挂载不同的组件，使用 is特性来实现。
需求：通过点击不同按钮切换不同视图
```
    <div id="app">
        <button @click="handleView('A')">第一句</button>
        <button @click="handleView('B')">第二句</button>
        <button @click="handleView('C')">第三句</button>
        <button @click="handleView('D')">第四句</button>
        <component :is="thisView"></component>
    </div>
    <script src="https://cdn.jsdelivr.net/npm/vue@2.5.16/dist/vue.js"></script>
    <script>
        Vue.component('compA',{
            template:'<div>离离原上草</div>'
        })
        Vue.component('compB',{
            template:'<div>一岁一枯荣</div>'
        })
        Vue.component('compC',{
            template:'<div>野火烧不尽</div>'
        })
        Vue.component('compD',{
            template:'<div>春风吹又生</div>'
        })
        var app = new Vue({
            el:'#app',
            data:{
                thisView:'compA'
            },
            methods:{
                handleView:function(tag){
                    this.thisView='comp' + tag;
                }
            }
        })
    </script>
```