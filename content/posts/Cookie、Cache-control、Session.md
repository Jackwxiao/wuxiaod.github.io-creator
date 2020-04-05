---
title: "HTTP之Cookie、Cache-control、Session"
date: 2019-09-26T11:17:26+08:00
draft: false
---
[HTTP请求与响应](https://www.jianshu.com/p/1938d3c995d2)

## 前言：

URI : 统一资源标识符,一个URI 可能包含 协议、登录信息、服务器地址、服务器端口、带层次的文件路径、查询参数等
URL :  统一资源定位符，是URI 的子集。

HTTP 协议是无状态协议，即HTTP 协议自身不会对请求和响应之间的通信状态进行保存。以确保协议的可伸缩性。
HTTP/1.1 虽然是无状态协议，但为了实现保持状态功能，于是就引入了 Cookie 技术用来管理状态。

## Cookie

#### Cookie 是什么？

1. Cookie 是浏览器访问服务器后，服务器传给浏览器的一段数据。
2. 浏览器需要保存这段数据，不得轻易删除。
3. 此后每次浏览器访问该服务器，都必须带上这段数据。

#### Cookie特点：

1. 服务器通过 Set-Cookie 头设给客户端一串字符串
2. 浏览器（客户端）得到Cookie 之后，每次访问相同域名的网页时都要带上这段字符串
3. 服务器读取Cookie就知道登录用户的信息。客户端要在一段时间内保存这个Cookie。

**cookie会被用户更改，所以要使用session来阻止用户更改。**

#### 什么是Session?
服务器通过Cookie给用户一个session id,session id对应服务器里面的一小块内存，每次用户访问服务器时，服务器通过session id去读取对应的session就知道用户的隐私信息了。

![cookie、session.png](https://i.loli.net/2019/10/22/iJAR95E128p4vmX.png)

![localStorage、sessionStorage.png](https://i.loli.net/2019/10/22/Ou7Apy2P6RDVLaw.png)


Session是基于Cookie实现的，反过来说，cookie是session的基石。
#### cookie和localstorage的区别：

* localstorage和http无关，不会被带到服务器上，cookie每次请求的时候会被浏览器带到服务器上；

* localstorage最大存储量为5Mb左右,cookie为4k左右。

#### localstorage和sessionstorage的区别：

* sessionstroage在用户关闭页面后就失效（会话结束后），localstorage永久有效。

* cookie默认在用户关闭页面后就失效，后台代码可以任意设置Cookie的失效期限。

#### Cache-control
**你想要哪个请求被缓存，就给一个Cache-Control**
 `response.setHeader('Cache-Control','max-age=30')//30秒内请求时返回的是内存给你的`
  
 但是首页不能设置Cache-Conrol,如果首页都缓存了，用户在缓存时间内无法获得最新内容。
 
![Cache-control.png](https://i.loli.net/2019/10/22/o2a91Bmiyzu7r5l.png)
 
 Cache-Control可以让浏览器在一段时间内不访问浏览器，直接用本地的内存或硬盘作为响应，可以极快的请求到资源。 如果要更新，只要在入口（html）文件里把URL变一下，跟以前的所有URL不一样就不会使用缓存，会把最新版的缓存下来，如果又过时了，再改一下就可以了。
 #### Expires和Cache-Control的区别：

 * Cache-Control是多长时间过期，Expires是什么时候过期，也就是设置的时候，C是设置时长，E设置时间点。
  
 #### cache-control与Etag:
 设置了Cache-Control直接不请求,Etag会请求但如果MD5一样就不会下载。
 

登陆注册图解：
![登陆注册图解.png](https://i.loli.net/2019/10/22/aXdWhxKSziBkpTr.png)