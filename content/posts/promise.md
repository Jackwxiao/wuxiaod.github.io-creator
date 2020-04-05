---
title: "Promise"
date: 2020-03-22T19:17:26+08:00
draft: false
---
## 异步与Promise
如果 JS 不能直接拿到一个函数的结果，可以先去执行别的代码，等结果到了再取结果，这就是异步<br>
异步的结果可以通过轮询获取，轮询就是定时去询问结果拿到了没有<br>
异步的结果可以通过回调获取，一般来说结果会被作为回调的第一个参数<br>
异步的好处是可以把用来等待的时间拿去做别的事情

#### promise的由来

>promise 是现在前端解决异步问题的统一方案！！！！<br>
>Promise 不是前端发明的<br>
>window.Promise 是一个全局函数，可以用来构造 Promise 对象<br>
>使用 return new Promise((resolve, reject)=> {}) 就可以构造一个 Promise 对象<br>
>构造出来的 Promise 对象含有一个 .then() 函数属性<br>
>resolve 和 reject 都只接受一个参数<br>
>resolve 和 reject 并不是 .then(succes, fail) 里面的 success 和 fail，resolve 会去调用 >success，reject 会去调用 fail
插一道面试题：

```
const array=  ['1','2','3'].map( parseInt）

console.log( array )
// [1,NaN,NaN]
```

只能这样写：

```
const array=  ['1','2','3'].map( item =>parseInt(item)）

console.log( array )
// [1,2,3]
```

异步任务拿不到结果，于是传一个回调给异步任务，异步任务完成时调用回调，调用的时候把结果作为参数。
如果有两结果呢，比如成功和失败

* 可以接收两个参数

```
fs.readFile('./1.txt', ( error, data )=>{
    if( error ){
        console.,log('失败')；return
    }
    console.log(data.toString())//成功
})
```

* 搞两个回调
  
满足某些条件的函数才被称为回调，比如我写一个函数 A，传给另一个函数 B 调用，那么函数 A 就是回调<br>
回调可以用于同步任务，不一定非要用于异步任务<br>
有的时候回调还可以传给一个对象，如 request.onreadystatechange，等待浏览器来调用

```
    aiax('get', '1.json', data=>{ }, error=>{ })
    //前面函数是成功调用，后面失败调用
   
   ajax( 'get', '1.json' , {
    success: () =>{ },  fail: ()=>{ }
   })
   //接收一个对象，对象有两个 key 表示成功和失败
```

上面方法都有问题：

1. 不规范，名称五花八门
2. 容易出现回调地狱，代码看不懂
3. 很难进行错误处理

怎么解决这三个问题？
-规范回调的名字或顺序；拒绝回调地狱，让代码可读性更强；很方便地捕获错误。

#### 以 AJAX 的封装为例来逐步讲解 Promise

首先是 用回调来封装

```
    ajax = (method, url, options)=>{
        const { succes, fail  } = options   //es6 析构赋值
        const request = new XMLHttpRequest()
        request .open(method, url)
        request.onreadystatechange = () =>{
            if(request.readyState = == 4){
              if(request.status < 400){
                success.call( null , request.response)
            }else if(request.status >= 400 ) {
                fail.call( null,request,request.status)
              }
        }
    }
    request.send()
} 

//封装完之后，使用ajax就只用写下面几句了

ajax('get', '/xxx',{
success(response){} , fail: (request,status) = >{}
})

```

promise 说这代码太蠢了。。。。

**用promise 改写**

```
ajax = (method,url , options)=>{ 
return new Promise((resolve,reject)=>{
    const { succes,fail } = options
    const request = new XMLHttpRequest() 
    request .open(method, url) 
    request.onreadystatechange = () =>{ 
         if (request.readyState = == 4){ 
            if(request.status < 400){ 
    resolve.call( null , request.response) 
        }else if (request.status >= 400){ 
        reject.call( null,request) 
            } 
        } 
    } request.send( )
})
 }
   
   aiax('get','/xxx')
   .then( (response)=>{ } , (request)=>{ } )
    //虽然也是回调，但不用记 success 和 fail  了 ，then 的第一个参数就是 success , 第二个参数是 fail
    //ajax 返回一个含有 .then( )  方法的对象
```

上面封装ajax 的缺点：

1. post 无法上传数据
`rquest.send(这里可以上传数据)`
2. 不能设置请求头
`request.setRequestHeader(key,value)`

可以使用 jQyery.ajax 或者 axios

不需要掌握jquery.ajax 了，现在都一般用 axios

axios 抄袭了jquery.ajax
它是一个专门用于操作 AJAX 的库
axios.get('/xxx') 返回一个 Promise 对象axios.get('/xxx').then(s, f) 在请求成功的时候调用 s，失败的使用调用 f

#### 用 Promise 对象实现 Ajax 操作(完善)

```
const getJSON = function(url) {
  const promise = new Promise(function(resolve, reject){
    const handler = function() {
      if (this.readyState !== 4) {
        return;
      }
      if (this.status === 200) {
        resolve(this.response);
      } else {
        reject(new Error(this.statusText));
      }
    };
    const client = new XMLHttpRequest();
    client.open("GET", url);
    client.onreadystatechange = handler;
    client.responseType = "json";
    client.setRequestHeader("Accept", "application/json");
    client.send();

  });

  return promise;
};

getJSON("/posts.json").then(function(json) {
  console.log('Contents: ' + json);
}, function(error) {
  console.error('出错了', error);
});
```

**getJSON是对 XMLHttpRequest 对象的封装，用于发出一个针对 JSON 数据的 HTTP 请求，并且返回一个Promise对象。需要注意的是，在getJSON内部，resolve函数和reject函数调用时，都带有参数。**

#### 手写Promise
```
function xxx(){
return new Promise((f1,f2)=>{
doSomething()
setTimeout(()=>{
//成功就调用f1，失败就调用f2
}，3000)
})
}
xxx().then(success,fail)
```