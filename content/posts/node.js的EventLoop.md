---
title: "node.js 的 Event loop"
date: 2019-11-16T10:36:04+09:00
draft: false
---

### node.js

node.js做三件事：

1. 初始化 event loop
2. 执行脚本
3. 运行 event loop

node.js中的 event loop :

timers阶段处理 setTimeout()、setInterval() 中该调的回调函数，到了调用时间就调用，没到时间就进入下一阶段（poll轮询阶段），

[图解一.png](https://i.loli.net/2019/12/14/3EYkncfK2WtHLmb.png)

 一般是停留在poll阶段。poll阶段处理除了timers和check阶段的一切事，比如操作系统返回”文件读完了“poll就去处理文件拿出数据放到对应的回调，
 给回调执行。check 阶段只处理setImmediate()

next.Tick()存在时，永远是它先执行。一般情况下是setTimeout()先于setImmediate()执行。

[处理机制.png](https://i.loli.net/2019/12/14/wx5loRp81bfEsdI.png)

setTimeout（fn, ）中的函数放在 timers阶段，setImmediate(fn2) 放在check阶段，process.next.Tick(fn3)放在当前阶段的后面。

----------------

相关试题：（建议画图做题）

[题1.png](https://i.loli.net/2019/12/14/tz5aiIQWSZ2reD7.png)

[题2.png](https://i.loli.net/2019/12/14/Oae1YBPQUch95nd.png)

** 先打印出 script start 、script end 然后是promise1、promise2最后才是setTimeout **

[题2输出结果](https://i.loli.net/2019/12/14/QdrpELFIe8jX4xR.png)

----------------

### 宏任务与微任务

Chrome中：

setTimeout ----> 一会儿（宏任务）macroTask
promise .then(fn) ------> 马上做（微任务）microTask
new promise( fn )，这个fn 是马上执行的，不用放到哪个队列里去。

[宏任务与微任务.png](https://i.loli.net/2019/12/14/oci9XnOvQpUEg2T.png)

resolve 只是决定执行 then 的第一个函数还是第二个函数。resolve 执行第一个任务。
then 的时候就把 then 后面的 f函数 加入 马上做（微任务） 队列中。await 就把它改写成 promise.then( f )

两者具体实现：

macrotasks: setTimeout 、setInterval 、setImmediate 、 I/O UI渲染
microtasks: promise 、 process.nextTick 、 Object.observe 、mutationObserve







