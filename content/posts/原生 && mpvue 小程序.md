---
title: "原生 || mpvue 小程序"
date: 2019-11-27T14:56:04+08:00
draft: false
---

### 小程序：

1. 无需下载，用完即走（体积小）
2. 同App  互补，可以实现 App 基本功能。
3. 微信扫一扫即可下载。
4. 开发周期短，成本较低。

#### 小程序适配方案：

Iphone6：1rpx = 1物理像素 = 0.5px

微信提供的换算方式：

1. 以Iphone 6 的物理像素个数为标准： 750；
2. 1rpx= 目标设备宽度 / 750 * px;
3. 此时底层已做 viewport 适配处理。

#### 重要文件：

 注册小程序：App ()
 
 注册页面：Page ()
 
app.js ： 入口文件；

app.json ：全局配置文件；

#### 事件：

* 冒泡事件：事件向父组件冒泡，bind事件绑定不会阻止冒泡事件向上冒泡；

* 非冒泡事件：catch 事件绑定可以阻止冒泡事件向上冒泡。

#### 生命周期函数：

onLoad : 做一些初始化工作，发送请求，开启定时器。
onReady : 监听页面初次渲染成功。
onShow : 监听页面显示。
...

#### 模板（template）

1. 定义： template属性： name（标识模板)
2. 使用：template属性： is(模板的 name)
3. 引入模板结构
4. 引入模板样式 @import '路径'
5. 传参 : data = '{{...item}}'
 
#### 列表渲染：

1. wx：for
2. wx:key 为每个个体元素进行标记
3. 遍历的个体： item
4. 遍历的下标 : index

#### 本地缓存

setStorage、getStorage

1. 缓存的是用户是否收藏当前文章
2. 注意：

 * 缓存之前应该先去获取之前本地缓存的数据

 * 缓存的新数据是在原有数据的基础上进行的

 * 当页面加载的时候 onLoad 中获取本地缓存的数据（动态修改当前页面是否收藏文章的状态）

 * 如果storage 中没有缓存过，通过key 获取的value为空，如果没有缓存过则需要初始化一个空对象在storage中

 #### 媒体：

 playBackgroundAudio（{url,
 title}）

 [媒体相关文档](https://developers.weixin.qq.com/miniprogram/dev/component/audio.html)

 监听：
 onplayBackgroundAudioPlay()

 [on...](https://developers.weixin.qq.com/miniprogram/dev/api/media/background-audio/wx.onBackgroundAudioStop.html)

 **mpvue对vue-router的支持不够好，问题较多，进行页面跳转可以使用小程序原生API：**

* wx.navigateTo() : 保留当前页面，可回退；
* wx.redirectTo()  : 不保留，不能回退；
* wx.switchTab()  ： 使用于 tabBar 页面。

#### 原生小程序与 mpvue 比较 ：

1. 原生小程序运行更稳定，兼容性号，mpvue在某些地方存在兼容性问题？

2. mpvue 支持组件化开发，效率高，功能强大（vuex，数据响应式）

3. mpvue 可基于 webpack 组件化，工程化开发；

4. 原生不支持 npm 安装包，不支持 css 预处理；

5. mpvue支持computed 计算属性和 watcher 监听器；模板语法只支持简单的js 表达式。可以直接写 div 、span 等标签。


-----------

[笔记链接 :)](https://app.yinxiang.com/fx/37884a3c-a828-405e-8c29-96b95e066717)