---
layout: default
title: Js Function
parent: JavaScript
grand_parent: Front-end Dev
nav_order: 14
---

 Here are JavaScript function examples .
{: .fs-6 .fw-300 }

1. TOC
{:toc}


# 一、function 简介


## 1、含义

**函数**：是一般是由事件驱动的，为了实现特定功能的，可以重复调用的一段可以执行的代码块。

## 2、分类

内置函数：字符串，数学函数，数组函数等，js 系统给我们提供好的函数。

自定义函数：根据需要定义的函数。

## 3、函数的类型

- 无参无返回值
- 无参有返回值
- 有参无返回值
- 有参有返回值

基本语法

```
function fn(){
    return 1 ;
}
```

示例：

```html
<script>
function noArgsReturn() {
    console.log("我是无参无返回值函数");
}

function noReturn(a) {
    console.log("我是有参无返回值函数，收到参数" +a);
}

function noArgs() {
    console.log("我是无参有返回值函数");
    return result;
}

function add(a, b) {  
    console.log("我是有参有返回值函数");
    return a + b;
}
</script>
```

return 语句将终止当前函数并返回当前函数的值，return 后面的语句不执行。


return 后面可以跟一个 value ，可以是 Js 中的任何数据类型，数字，字符串，函数，对象等，当然也可是再返回一个函数。


```html
<script>
function getObj() {
    console.log("我是无参有返回值函数");
    return {
        "name": "xiaocai"
    };
}

function getFunc(a, b) {  
    console.log("我是有参有返回值函数");
    return function() {
       return a - b ;
    };
}
</script>
```

**boolean类型的返回**

- return true; 返回正确的处理结果。
- return false; 返回错误的处理结果，终止处理。return false 只在当前函数有效，不会影响其他外部函数的执行。
- return ; 把控制权返回给页面。(返回 undefined)


## 4、void 的使用


`javascript:void(0);` 该操作符指定要计算一个表达式但是不返回值。

`void()` 仅仅是代表不返回任何值，但是括号内的表达式还是要运行: 

```javascript
void(alert("running!"));
```

`href="#"`与 `href="javascript:void(0)"`的区别

`#` 包含了一个位置信息，默认的锚是#top 也就是网页的上端。

`javascript:void(0)`, 仅仅表示一个死链接。


##  5、匿名函数


匿名函数：在 JavaScript 中，当把函数当做数据使用时，可以不设置名字。

函数必须要先声明后使用。如果后声明后自动进行函数提升。

```html
<script>
var sum = function(a, b) {
  return a + b;
}

console.log(sum(1,2));

console.log(sub(10, 8));
var sub = function(a, b) {
  return a - b;
}

</script>
```

##  6、回调函数

什么是回调函数？

当一个函数作为另一个函数的参数时，作为参数的函数被称之为回调函数。


```html
<script>
var obt =document.querySelector("button");
//回调函数
obt.onmouseover = function(){ 
    alert("回调函数被触发！");
}

//延时器：按照指定的时间之后，执行函数表达式
setTimeout(function(){
    console.log("2秒之和你会见到我！");
},2000)


var arr =["red","blue","green"];

arr.forEach(function(v, i, arr){
    console.log(v, i, arr);
});
</script>
```

## 7、函数的自调用(沙箱)

function 前面加上一些操作符，这样 js 引擎在解析的时候就不会把它当成是函数声明了。


```html
<script>
//匿名函数
var f1 = function(){
    console.log("立即执行1" +111);
}();


(function(){
    console.log("立即执行2" + 222);
}());

(function(){
    console.log("立即执行3" + 333);
})();

!function(){
     console.log("立即执行4" + 444);
 }();

//函数的声明
+function f1(){
    console.log("立即执行5" + 555);
}();


</script>
```

沙箱：就是与外界隔绝的一个环境，外界无法修改该环境内的任何信息（沙箱内的东西单独属于一个世界）。

JS 中的沙箱模式：还是通过函数来实现的

（1）沙箱模式的基本模型：自调用函数

```
(function(){
var a = 123; //外面并不能访问到这个 a
})();
```
（2）沙箱里面的变量对外面没有影响

（3）为什么要使用立即执行函数表达式 IIFE（其实就是匿名函数）来写沙箱：因为立即 IIFE 不会在外界暴露任何的全局变量，但是又可以形成一个封闭的空间，刚好可以实现沙箱模式。

（4）在沙箱中将所有变量的定义放在最上面，然后中间就放一些逻辑代码。

（5）js 中沙箱模式的实现原理：函数可以构建作用域，上级作用域不能直接访问下级作用域中的数据。


```html
<script>
// r 是形式参数
(function(r){
    var area = Math.PI * r * r;
    console.log(area);
})(5);
// 5 是实际参数
</script>
```

## 8、Function 构造函数（函数声明）


Function 是一个构造器， 能创建 Function 对象 ，即 JavaScript 中每个函数实际上都是 Function 对象。

函数构造器的语法：
```
new Function ([arg1[, arg2[, ...argN]],] functionBody)
```
说明：
- arg1、arg2 等为构造器的参数，
- functionBody 为方法体。注意：参数必须用引号包围！

实例：

```javascript
var plus = new Function("a","b","return a+b");
var result = plus(1,2);//调用，返回 3

```
上述方法创建 function 等同于普通函数声明方法：

```javascript
function plus(a,b){return a+b;};
```

```javascript
var add = new Function(
'x',
'y',
'return x + y');
```

不同方式创建函数

```javascript

//字面量方式创建函数
var fun =function(){
    console.log(100)
}

//函数声明方式创建函数
function fn(){
    console.log(200);
}
//创建Funtion类型的对象var函数名=newFunction（‘参数'，"`函数体）
var f = new Function('a','console.log(a)');
f(2);//以函数方式调用

var f = new Function('a','b','var c = a+b ; console.log(c); return c;');
f(2);//以函数方式调用
```

## 8、Function 与 function 的区别

**Function 与 function 的区别**

- Function 是一个功能完整的对象，作为 JS 的内置对象之一 。 

- function 只是一个关键字，用来创建一个普通函数或对象的构造函数。

- JS 的普通函数都是 Function 对象的实例，所以函数本身也是一个对象，就像 var 一样，只不过这个对象具有可调用特征而已。



## 9、递归函数

递归函数：函数自己调用自己

```javascript
var n = 1;

function f1(){
    console.log(n);
    n++;
    if(n>80){
        return 0;//调用终止
    }
    f1();
}

f1();
```

## 10、默认值和函数参数

Es6 的参数默认值

```javascript
function log(x, y="lisi"){
    console.log(x,y);
}
log("hello");//hello,Lisi
log("hello","zhangsan");//heLLo,zhansan
```

Es5 的参数默认值

```javascript
function log(x, y){
    y = y || "lisi";
    console.log(x,y);
}
log("hello");//hello,Lisi
log("hello","zhangsan");//heLLo,zhansan
```

参数是一个函数

```javascript
//参数是函数
function f1(fn){
    console.log("我是函数f1");
    fn()
}

f1(function(){
    console.log("我是实际参数函数");
});
```

## 11、高阶函数

高阶函数英文叫 Higher-order function。

那么什么是高阶函数？

JavaScript 的函数其实都指向某个变量。

既然变量可以指向函数，函数的参数能接收变量，那么一个函数就可以接收另一个函数作为参数，这种函数就称之为高阶函数。




# 二、函数的作用域和作用域链

## 1、作用域


作用域（scope）就是变量和函数的可访问范围，或者说变量或函数起作用的区域。

```javascript
//f1函数是全局作用域
function f1(){
    console.log("我是f1");
    //f2是局部作用域：只能在函数内部调用
    function f2(){
        console.log("我是f2");
}
f1();
f2();//报错
```


## 2、作用域分类

Javascript 只有两种作用域

（1）全局作用域

全局作用域：变量和函数在整个程序中一直存在，所有地方都可以读取。

全局变量(global variable)：在函数外部声明的变量，它可以在函数内部读取。

那么什么情况下声明为全局变量呢，多个函数共同使用。

（2）局部作用域

局部作用域：变量和函数只在函数内部存在。在函数外部变量失效。

和作用域与之对应的，javascript 中有两种变量：

局部变量(local variable)：在函数内部定义的变量，外部无法读取。

在函数中，参数也是局部变量。

函数也有作用域，声明在外部的函数，可以任意位置调用，声明在函数内部的函数，一般只能在内部调用。

局部变量会在函数运行以后被删除。

全局变量会在页面关闭后被删除。


**变量加 var 和不加 var 的区别**


1.在函数作用域内加 var 定义的变量是局部变量，不加 var 定义的就成了"全局变量"。

2.在全局作用域下，使用 var 定义的变量不可以 delete,没有 var 定义的变量可以 delete。也就说明隐式全局变量严格来说不是真正的变量，而是全局对象(window)的属性，因为属性可以通过 delete 删除，而变量不可以。

3.使用 var 定义的变量不赋值时会有一个默认初始值：undefined，而不使用 var 定义的变量在 alert()时浏览器会给出错误信息：a is not defined  。

4.在 ECMASceipt5 的'use strict'模式下，如果变量没有使用 var 定义，会报错，

