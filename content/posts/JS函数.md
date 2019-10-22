---
title: "JS函数"
date: 2019-09-28T23:01:04+08:00
draft: false
---
###函数
指可以执行代码的对象，和数组一样有不一致性。
#####三种函数声明方式

* function  f() { }
  
* var f = function f()  { }; //注意分号
  
* f  = new Function ('x','y','return x+y')   //只有最后一个参数会被当作函数体，可以不使用new 命令，不推荐使用这种声明方式
  
注意：如果同一个函数被多次声明，后面的声明会覆盖前面的声明。也就是说起前面的声明会失效。

**函数名的提升**

JS引擎将函数名视同变量名，使用function 声明函数时，整个函数回想变量声明一样被提升到代码头部。如果同时使用function命令和赋值语句声明同一个函数，左后总是采用赋值语句的定义

```
var f = function () {
  console.log('1');
}
function f() {
  console.log('2');
}
f() // 1
```
**函数的属性和方法**

* name :`f.name`返回函数的名字，如果通过变量赋值定义的匿名函数则返回变量名。具名函数返回`function`关键字后的函数名。
* 
```
var f3 = function myName() {};
f3.name // 'myName'
```
上面代码中，f3.name返回函数表达式的名字。注意，真正的函数名还是f3，而myName这个名字只在函数体内部可用。

*用处：可用来获取参数函数的名字。*

* length:   `f.length` 返回函数预期传入的参数个数。
  
* toString: 返回一个字符串，内容是函数源码。
  
**作用域**

作用域（scope）指的是变量存在的范围。在 ES5 的规范中，JavaScript 只有两种作用域：一种是全局作用域，变量在整个程序中一直存在，所有地方都可以读取；另一种是函数作用域，变量只在函数内部存在。ES6 又新增了块级作用域。
对于顶层函数来说，函数外部声明的变量就是全局变量（global variable），它可以在函数内部读取。
在函数内部定义的变量，外部无法读取，称为“局部变量”（local variable）。

*函数内部定义的变量，会在该作用域内覆盖同名全局变量。函数内部的变量也会提升。*

**参数**

* 参数可以省略，但没办法指省略靠前的参数而保留后面的参数，若必须省略靠前的参数，只能显示传入 `undefined`。
  
* 传递方式：函数参数如果是原始类型的值，传递方式是传值传递，这意味着在函数体内部修改参数值不会影响到函数外部。如果函数参数是复合类型的值，传递方式是传址传递，也就是说，传入函数的原始值的地址，因此在函数内部修改参数将会影响到原始值。
  
```
var obj = { p: 1 };
function f(o) {
  o.p = 2;
}
f(obj);
obj.p // 2
```
>上面代码中，传入函数f的是参数对象obj的地址。因此，在函数内部修改obj的属性p，会影响到原始值。
>
注意，如果函数内部修改的，不是参数对象的某个属性，而是替换掉整个参数，这时不会影响到原始值。如果有同名函数，则取最后出现出现的值。

```
function f(a, a) {
  console.log(a);
}

f(1) // undefined
```
```
function f(a, a) {
 console.log(arguments[0]);
}
f(1) // 1
```
**arguments对象**

* `arguments`对象包含了函数运行时的所有参数。`arguments[0]`就是第一个参数，`arguments[1]`就是第二个参数，以此类推。这个对象只有在函数体内部，才可以使用。
  
正常模式下，`arguments`对象可以在运行时修改。
```
var f = function(a, b) {
  arguments[0] = 3;
  arguments[1] = 2;
  return a + b;
}

f(1, 1) // 5
```
严格模式下，`arguments`对象与函数参数不具有联动关系。也就是说，修改`arguments`对象不会影响到实际的函数参数。
```
var f = function(a, b) {
  'use strict'; // 开启严格模式
  arguments[0] = 3;
  arguments[1] = 2;
  return a + b;
}

f(1, 1) // 2
```
上面代码中，函数体内是严格模式，这时修改`arguments`对象，不会影响到真实参数a和b。
* 需要注意的是，虽然`arguments`很像数组，但它是一个对象。数组专有的方法（比如slice和forEach），不能在arguments对象上直接使用。
如果要让`arguments`对象使用数组方法，真正的解决方法是将`arguments`转为真正的数组。
下面是两种常用的转换方法：slice方法和逐一填入新数组。
```
var args = Array.prototype.slice.call(arguments);

// 或者
var args = [];
for (var i = 0; i < arguments.length; i++) {
  args.push(arguments[i]);
}
```
**闭包**
一个函数如果使用了它范围外的变量，那么这个函数+这个变量就是一个闭包。
```
function f1() {
  var n = 999;
  function f2() {
    console.log(n);
  }
  return f2;
}

var result = f1();
result(); // 999
```
上面代码中，函数f1的返回值就是函数f2，由于f2可以读取f1的内部变量，所以就可以在外部获得f1的内部变量了。

闭包就是函数f2，即能够读取其他函数内部变量的函数。由于在 JavaScript 语言中，只有函数内部的子函数才能读取内部变量，因此可以把闭包简单理解成“定义在一个函数内部的函数”。闭包最大的特点，就是它可以“记住”诞生的环境，比如f2记住了它诞生的环境f1，所以从f2可以得到f1的内部变量。在本质上，闭包就是将函数内部和函数外部连接起来的一座桥梁。

闭包的最大用处有两个，一个是可以读取函数内部的变量，另一个就是让这些变量始终保持在内存中，即闭包可以使得它诞生环境一直存在。请看下面的例子，闭包使得内部变量记住上一次调用时的运算结果。
本文内容摘自（[https://wangdoc.com/javascript/types/function.html](https://wangdoc.com/javascript/types/function.html)
）
