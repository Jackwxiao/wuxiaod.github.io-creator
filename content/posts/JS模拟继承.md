---
title: "JS模拟继承"
date: 2019-10-22T19:17:26+08:00
draft: false
---
### JS模拟继承
继承：面向对象编程中的一个概念，继承可以使子类具有父类的各种属性和方法。

能产生对象的东西即为类。
`var  person = new Human()`中的person就是一个对象，所以Human()便是一个类。
但是父类Object（）到子类Human（）是一个自带的继承。
#### ES5写继承：
```
function Human(name){     //父类 Human()
    this.name = (name) //自有属性name
}
Human.prototype.run = function(){ //共有属性为run
    console.log(“我叫”+this.name",我有run的属性")
    return undefined
}

function Man(name){  //声明 Man()
    Human.call(this, name) //call 父类
    this.gender = '男'   //声明自身的属性gender
}

var f = function(){ }
    f.prototype = Human.prototype
    man.prototype = new f( )
    
    Man.prototype.fight = function(){
    console.log('如来神掌')
    }
```

![继承.png](https://i.loli.net/2019/10/22/861syzlNhKfdG7E.png)

这三句：
```
var f = function(){ }
    f.prototype = Human.prototype
    man.prototype = new f( )
```
等价于：
```
Man.prototype.__proto__ = Human.prototype  //Man的原型的原型等于Human的原型

IE不支持上面代码，所以用上面的三句话代替。
```

![原型链的继承.png](https://i.loli.net/2019/10/22/jvCDkybAQ983BMe.png)

***

![为何要一个空对象图解.png](https://i.loli.net/2019/10/22/F3m1WsVjebdc8OZ.png)

但是如果按上图红字那样写`Man prototype  =new Human()`虽然可以达到插入原型链的目的，即`Man.prototype.__proto__ = Human.prototype`
但会出现不该出现的东西如下图所示：这是因为上图第四句，执行了this.name = name。所以就有了那三句话代替这一句。。

![出错图.png](https://i.loli.net/2019/10/22/utlJdCf4m7IThFA.png)

#### ES6写法：

[MDN关于 类 的解析](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Classes)

```
class Human{
     constructor(name){
         this.name = name
     }
     run(){
         console.log("我叫"+this.name+"，我有run的属性")
         return undefined
     }
 }
 class Man extends Human{
     constructor(name){
         super(name)
         this.gender = '男'
     }
     fight(){
         console.log('如来神掌')
     }
 }
 ```
