---
title: "JS模拟继承"
date: 2019-10-04T19:17:26+08:00
draft: false
---
### JS模拟继承
继承：面向对象编程中的一个概念，继承可以使子类具有父类的各种属性和方法。

能产生对象的东西即为类。

* 定义一个类的一种方法是使用一个类声明。要声明一个类，使用带有class关键字的类名;

* 一个类表达式是定义一个类的另一种方式。类表达式可以是被命名的或匿名的。赋予一个命名类表达式的名称是类的主体的本地名称。

* constructor方法是一个特殊的方法，这种方法用于创建和初始化一个由class创建的对象。一个类只能拥有一个名为 “constructor”的特殊方法。如果类包含多个constructor的方法，则将抛出 一个SyntaxError 。一个构造函数可以使用 super 关键字来调用一个父类的构造函数。

* 函数声明和类声明之间的一个重要区别是函数声明会提升，类声明不会。

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

使用 extends 创建子类 :
如果子类中存在构造函数，则需要在使用“this”之前首先调用 super()。

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
------------

#### Object.create() 实现类继承： 

Object.create()方法创建一个新对象，使用现有的对象来提供新创建的对象的__proto__

```
// Shape - 父类(superclass)function Shape() {
  this.x = 0;
  this.y = 0;}

// 父类的方法Shape.prototype.move = function(x, y) {
  this.x += x;
  this.y += y;
  console.info('Shape moved.');};

// Rectangle - 子类(subclass)function Rectangle() {
  Shape.call(this); // call super constructor.}

// 子类续承父类Rectangle.prototype = Object.create(Shape.prototype);Rectangle.prototype.constructor = Rectangle;

var rect = new Rectangle();

console.log('Is rect an instance of Rectangle?',
  rect instanceof Rectangle); // true
console.log('Is rect an instance of Shape?',
  rect instanceof Shape); // true
rect.move(1, 1); // Outputs, 'Shape moved.'
```

如果你希望能继承到多个对象，则可以使用混入的方式。
```

function MyClass() {
     SuperClass.call(this);
     OtherSuperClass.call(this);}

// 继承一个类MyClass.prototype = Object.create(SuperClass.prototype);// 混合其它
Object.assign(MyClass.prototype, OtherSuperClass.prototype);// 重新指定constructorMyClass.prototype.constructor = MyClass;

MyClass.prototype.myMethod = function() {
     // do a thing};
```
Object.assign 会把  OtherSuperClass原型上的函数拷贝到 MyClass原型上，使 MyClass 的所有实例都可用 OtherSuperClass 的方法。Object.assign 是在 ES2015 引入的，且可用 polyfilled。要支持旧浏览器的话，可用使用 jQuery.extend() 或者 _.assign()。


#### 使用 super 调用超类

super 关键字用于调用对象的父对象上的函数。

```
class Cat { 
  constructor(name) {
    this.name = name;
  }
  
  speak() {
    console.log(this.name + ' makes a noise.');
  }}

class Lion extends Cat {
  speak() {
    super.speak();
    console.log(this.name + ' roars.');
  }}
```