---
title: "JSONP"
date: 2019-09-10T11:26:04+08:00
draft: false
---

### JSONP

JSONP是什么:

当浏览器不支持CORS跨域（IE不支持）时，必须通过其他方法进行跨域，通过请求 JS 文件，JS文件执行一个回调，回调里有我们请求的数据。回调函数名是可以随意设置的随机数，
创建script 请求js ，js 把数据夹带过来，然后就可以拿到js 数据了。

优点：支持IE 、可以跨域;
缺点：script标签读不到AJAX 的精准状态，也就是读不到状态码是什么，只知道成功或失败。

script 请求只能发get 请求,所以JSONP不能发post 请求.

JSONP是服务器与客户端跨源通信的常用方法。最大特点就是简单适用，老式浏览器全部支持，服务器改造非常小。<br>
它的基本思想是，网页通过添加一个script元素，向服务器请求JSON数据，这种做法不受同源政策限制；服务器收到请求后，将数据放在一个指定名字的回调函数里传回来。

### 如何向服务器发请求？

* 用 form 可以发请求，但是会刷新页面或新开页面

* 用 a 可以发 get 请求，但是也会刷新页面或新开页面
  
* 用 img 可以发 get 请求，但是只能以图片的形式展示
  
* 用 link 可以发 get 请求，但是只能以 CSS、favicon 的形式展示
  
* 用 script 可以发 get 请求，但是只能以脚本的形式运行
  
其中，通过动态创建 script 并添加 callback 查询参数来发 get 请求的就是 JSONP 
>请求方：frank.com 的前端程序员（浏览器）
>响应方：jack.com 的后端程序员（服务器）
>       1.请求方创建 script，src 指向响应方，同时传一个查询参数 ?callbackName=yyy
>        2.响应方根据查询参数callbackName，构造形如
>           i.yyy.call(undefined, '你要的数据')
>           ii.yyy('你要的数据')
>           这样的响应
>       3.浏览器接收到响应，就会执行 yyy.call(undefined, '你要的数据')
>      4.那么请求方就知道了他要的数据 
>这就是JSONP


### 用 script 造 get 请求

```
button.addEventListener('click', (e)=>{
    let script = document.createElement('script')
    script.src = '/pay'
    document.body.appendChild(script)
    script.onload = function(e){ // 状态码是 200~299 则表示成功
        e.currentTarget.remove()
    }
    script.onerror = function(e){ // 状态码大于等于 400 则表示失败
        e.currentTarget.remove()
    }
})
```

//后端代码

```
if (path === '/pay'){
    let amount = fs.readFileSync('./db', 'utf8')
    amount -= 1
    fs.writeFileSync('./db', amount)
    response.setHeader('Content-Type', 'application/javascript')
    response.write('amount.innerText = ' + amount)
    response.end()
}
```
**这种技术叫做 SRJ - Server Rendered JavaScript**

### JSONP

```
button.addEventListener('click', (e)=>{
    let script = document.createElement('script')
    let functionName = 'frank'+ parseInt(Math.random()*10000000 ,10)
    window[functionName] = function(){  // 每次请求之前搞出一个随机的函数
        amount.innerText = amount.innerText - 0 - 1
    }
    script.src = '/pay?callback=' + functionName
    document.body.appendChild(script)
    script.onload = function(e){ // 状态码是 200~299 则表示成功
        e.currentTarget.remove()
        delete window[functionName] // 请求完了就干掉这个随机函数
    }
    script.onerror = function(e){ // 状态码大于等于 400 则表示失败
        e.currentTarget.remove()
        delete window[functionName] // 请求完了就干掉这个随机函数
    }
})
```

//后端代码

```
if (path === '/pay'){
    let amount = fs.readFileSync('./db', 'utf8')
    amount -= 1
    fs.writeFileSync('./db', amount)
    let callbackName = query.callback
    response.setHeader('Content-Type', 'application/javascript')
    response.write(`
        ${callbackName}.call(undefined, 'success')
    `)
    response.end()
}

```

约定：

```
callbackName -> callback
yyy -> 随机数 frank12312312312321325()

 $.ajax({
 url: "http://jack.com:8002/pay",
 dataType: "jsonp",
 success: function( response ) {
     if(response === 'success'){
     amount.innerText = amount.innerText - 1
     }
 }
 })

 $.jsonp()

```
