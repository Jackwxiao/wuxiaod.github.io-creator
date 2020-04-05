---
title: "Vuex 从基础到深入"
date: 2020-03-03T21:29:04+08:00
draft: false
---

小范围数据共享方式：

1. 父向子传值： v-bind属性绑定
2. 子向父传值： v-on 事件绑定
3. 兄弟组件之间共享数据： eventBus

* 数据接收方定义 $on 事件

* 数据发送方定义 $emit 触发

## Vuex

Vuex 是实现组件全局状态（数据）管理的一种机制，可以方便的实现组件之间数据的共享。

[有无 Vuex 的区别图解](/static/images/article/有无%20Vuex%20的区别图解1.png)

## 使用 Vuex 统一管理状态的好处：

1. 能够在 Vuex 中集中管理共享的数据，已于开发和后期维护；
2. 能够高效实现组件之间的数据共享，提高开发效率；
3. 存储在 Vuex 中的数据都是响应式的，能够实时保持数据与页面同步。 

**一般情况下，只有组件之间共享的数据才有必要存储到 Vuex 中，对于组件中的私有数据，依旧存储在组件自身的 data 中即可。具体需求视情况而定**

## 基本使用
1. 安装依赖包
` npm install vuex --save `
2. 导入 Vuex 包
```
import Vuex from 'vuex'
Vue.use(Vuex)
```
3. 创建 store 对象
```
const store = new Vuex.Store({
    state: { count: 0 }
    // state 中存放的是全局共享的数据
})
```
4. 将 store 对象挂载到 Vue 实例中
```
 new Vue({
     el: '#app',
     render: h => h(app),
     router,
     // 将创建的共享数据对象挂载到 Vue 实例中，
     // 所有组件就可以直接从store 中获取全局数据了
     store
 })
```

### Vuex 核心概念

#### State

* State 提供唯一的公共数据源，所有的共享数据都要统一都要放到 Store 的 State 中存储

* 组件访问 State 中数据的方式一：

` this.$store.state.全局数据名称 `

* 组件访问 State 中数据的方式二：

```
// 1. 从 Vuex 中按需导入 mapState 函数
import { mapState } from 'vuex'
```
通过刚才导入的mapState函数，将当前组件需要的数据映射为当前组件的 computed 计算属性：
```
 // 2. 将全局数据映射当前组件的计算属性
 computed: {
     ...mapState(['count'])
 }
 // 3. 在template 模板中 使用插值表达式 {{count}} 渲染
```
**不允许组件直接修改 store 中的公有属性，只能通过 mutation 变更 store 数据 ，这样虽然繁琐但可以集中监控所有数据的变化**

#### Mutation
```
// 定义 mutation
 const store = new Store({
     state: {
         count: 0
     },
     mutations: {
        add(state) {
            state.count++
            <!-- 
            add(state, step) { 可接受参数step 
                state.count += step
            }
            -->
         }
     }
 })
// 触发 mutations
  methods: {
      handle(){ 
          // 方式一
          this.$store.commit('add') 
          <!-- 
          this.$store.commit('add', 3) 携带参数 3
          -->
      }
  }

```

触发mutations的第二种方式：

```
// 1. 从Vuex 中按需导入 mapMutations 函数
import { mapMutations } from 'vuex'
// 2. 将需要的mutations函数映射为当前组件的 methods 方法： 
 methods: {
     ...mapMutations(['add', 'addN']),
     handler(){
         this.add()
         // this.addN(3)
     }
 }
```

**commit 的作用就是调用某个 mutation 函数**

**mutations 函数中不能执行异步操作,于是有了下面的 Action** 

### Actions

Action 用于处理异步任务。如果通过异步操作变更数据，必须通过使用Action, 而不能使用Mutation,但在Action中还是要通过触发Mutation的方式间接变更数据。
```
 // 定义 action
 const store = new Vuex.Store({
     // ....代码省略
     mutations: {
         add(state){
             state.count++
         }
     },
     actions: {
         addAsync(context){ // 也可接受参数
             setTimeout (() => { // commit 触发某个mutation 函数 
                 context.commit('add'), 1000)
             }
         }
     }
 })
    // 触发 action 的方式一：
  methods: {
      handle(){
          // dispatch 用来触发某个 action 
          this.$store.dispatch('addAsync')
      }
  }
--------
    // 触发 action 的方式二：
    // 1. 从 vuex 中按需导入 mapActions 函数
 import { mapActions } from 'vuex'
    // 2. 将指定的 actions 函数映射为当前组件的 methods 函数：
  methods: {
      ...mapActions(['addAsync','addNSync']),
      handle(){
          this.addAsync()
      }
  } 
 ```

 *可以直接在template中添加  `<button @click="addAsync"> +1 async </button>` 而不需要定义 handle 事件 ，因为mapActions将actions中的异步函数映射为自己的 methods 了*

#### Getter

 Getter 用于对Store  中的数据进行加工处理形成新的数据。

 1. Getter 可以对 Store 中已有的数据加工处理之后形成新的数据，类似 Vue 的计算属性。
 2. Store 中数据发生变化，Getter 的数据也会跟着变化。

 ```
 // 定义 getter 
 const store = new Vuex.Store({
     state: {
         count: 0
     },
     getters: {
         showNum(state) {
             return '当前最新数据是【'+ state.count + '】' 
         }
     }
 })

 // 使用getters 的第一种方法：

 this.$store.getters.名称

 // 使用getters 的第二种方法：

import { mapGetters } from 'vuex'
computed:{
    ...mapGetters(['showNum'])
}
```
附： [ todolist : 一个使用 Vuex 完成状态管理的 demo](https://github.com/WUXIAOd/vue-todolist) 
