---
layout: default
title: Js 事件
parent: JavaScript
grand_parent: Front-end Dev
nav_order: 10
---


 Here are commonly used JavaScript examples .
{: .fs-6 .fw-300 }



1. TOC
{:toc}


# 一、事件概述

## 1、什么是事件

事件就是在文档中或者在浏览器窗口中通过某些动作触发。

比如，单击，鼠标经过，键盘按下等。事件通常和函数结合使用。

事件的作用：
- (1)各个元素之间可以借助事件来进行交互
- (2)用户和页面之间也可以通过事件来交互
- (3)后端和页面之间也可以通过事件来交互（减缓服务器的压力）Onsubmit(提交事件和表单一起使用)

## 2、事件分类

1、鼠标事件

onclick、ondblclick、onmouseover、onmouseout、onmousedown、onmou
seup、onmousemove

2、HTML 事件

onload、onscoll、onsubmit、onchange、onfoucs(获取焦点)，onblur(失去
焦点)

3、键盘事件

- onkeydown: 键盘按下时触发
- onkeypress:键盘按下并松开的瞬间触发
- onkeyup: 键盘抬起时触发

# 二、事件的使用（重点）

## 1、html 事件

绑定操作发生在 HTML 代码中的事件，称为 HTML 事件。

语法：on+事件=`函数();函数();函数();......`

HTML 事件的移除方式：

元素.setAttribute('on+事件名'，null);

HTML 事件缺陷：耦合性太强了，修改一处另一处也要修改。

当函数没有加载成功时，用户去触发事件，则会报错。

```html
<body>
<button onclick="f1();f2();">单击</button>
<script>
var obt=document.querySelector("button");
//事件清除的方法1
//obt.onclick = null;

//事件清除的方法2
//obt.setAttribute("onclick", null);

//事件清除的方法3
obt.removeAttribute("onclick");
function f1(){console.log("f1");}
function f2(){console.log("f2");}
</script>
</body>
```
## 2、DOM0 级事件

在 js 脚本中，直接通过【on+事件名】方式绑定的事件称为是 **DOM0 级事件**。

基本语法：
```
元素.on+事件名 = function(){ };

元素[on+事件名] = function(){ };
```
DOM0 级事件的移除方式：
```
元素. on+事件名=null;
```

**DOM0 事件调用函数的几种方式**：

- （1）`<input type=”button” onclick=”函数名”>`
- （2）节点.事件=function(){}
- （3）节点.事件=函数名

```html
<body>
<!-- 方式1  -->
<button onclick="f1();f2();">单击</button>
<script>
var obt=document.querySelector("button");
// 方式2 直接写函数
obt.onclick = function(){
    f1();
}

// 方式3 直接写函数名
//obt.onclick = f1 ;

function f1(){console.log("f1");}
function f2(){console.log("f2");}
</script>
</body>
```

## 3、DOM2 级事件

在 js 脚本中，通过 addEventListener 函数绑定的事件称为是 DOM2 级事件。

语法：
```
元素.addEventListener(type,listener,useCapture)
```
- type:事件类型。【没有 on！没有 on！没有 on！】
- listener:监听函数，绑定的函数
- useCapture:是否使用捕获机制。如果不写，默认值为 false
    - false:冒泡机制
    - true：捕获机制
注意:DOM2 级事件可以绑定多个函数，执行顺序按照函数书写的顺
序。

{: .tips }
>注意:DOM2 级事件可以绑定多个函数，执行顺序按照函数书写的顺序。

DOM2 级事件的移除方式:

```
node.removeEventListener(type,外部函数名,useCapture)
```

**事件流**

事件流：多个**嵌套的标记**或者包含的标记**拥有相同的事件**，其中一个元素的事件触发，同时影响其他元素同类型的事件的触发，我们称之为“事件流”。

事件流语法
方式 1：
```
对象.addEventListener(“事件类型”,”函数”,“事件流”); 
// 针对非 ie 浏览器的，事件类型：不加 on
```
方式 2：
```
对象.attachEvent(“事件类型”,”函数”,“事件流”); 
//针对 ie 浏览器的,事件类型：加 on
```

事件流有两种：

- 1、冒泡型（事件由内到外），默认值：false
- 2、捕捉型（事件由外到内）值：true


**冒泡**

什么是事件冒泡?

事件由具体某个元素（子节点）逐级向上传播（父节点，比如html）

**捕捉**

什么是事件捕捉？

事件由某个具体的元素（父节点）逐级向下传播（子节点）。


```html
<body>
<!-- 方式1  -->
<button onclick="f1();f2();">单击</button>
<script>
var obt=document.querySelector("button");
var outer = document.querySelector("#outer");
var inner = document.querySelector("#inner");
var core = documcnt.qucrySelector("#core");
// true : 表示事件捕捉由外向内
outer.addEventListener("click", function(){
    alert("outer")
}, true);
inner.addEventListener("click", function(){
    alert("inner")
} ,true);
core.addEventListener("click", function(){
    alert("core")
},true);
</script>
</body>
```

如何阻止冒泡

- stopPropagation(); 针对非 ie 浏览器阻止冒泡,阻止事件的派发
- cancelBubble=true; 针对 ie 浏览器


```html
<body>
<!-- 方式1  -->
<button onclick="f1();f2();">单击</button>
<script>
var outer = document. querySelector("#outer");
var inner = document.querySeleetor("#inner");
var core = document.querySelector("#core");

core.onclick = function(){
    alert("core!")
    event.stopPropagation();//方法1-阻止冒泡event：是js的内置事件对象
    event.cancelBubble=true; //方法2-阻此泡
}
inner.onclick = function(){
    alert("inner!")
    event.stopPropagation();//方法1-阻止冒泡
    event.cancelBubble = true; //方法2-阻止冒泡 
}
outer.onclick = function(){
    alert("outer!");
}

</script>
</body>
```

如何阻止默认行为？

- preventDefault()
- returnValue = false; 

```html
<!DOCTYPE html><html><headlang="en">
<meta charset="uTF-8">
<title></title>
<style></style>
</head>
<body>
<a href="http://www.baidu.com">百度</a>
<button>单击</button>
<form action="reg.php" method="post" name="form1">
用户名：<input type="text" name="username"/><br/>
密码：<input type="password" name="pwd"/><br/>
<input type="submit" value="提交表单"/>
</form>
<script>
var oa = document.querySelcctor("a");
var oform = document.querySelector("form");
var username = document. getElementsByName(username)[0];
oform.onsubmit = function(){
    if(username.value == ""){
        alert("用户名不能为空！");
        document.forml.uscrname.focus();//获取焦点
        event.preventDefault();//阻止默认提交
    }
    console.log("密码是："+document["form1"]["pwd"]["value"]):
}
oa.addEventListener("click",function(e){
    //event.preventDefault();/阻止默认链接-方法1
    e.preventDefault();
    e.returnValue=false ;//阻比默认链接-方法2
}
</script>
</body>
```
<a href="http://www.baidu.com">百度</a>
<button>单击</button>
<form action="reg.php" method="post" name="form1">
用户名：<input type="text" name="username"/><br/>
密码：<input type="password" name="pwd"/><br/>
<input type="submit" value="提交表单"/>
</form>
<script>
var oa = document.querySelcctor("a");
var oform = document.querySelector("form");
var username = document. getElementsByName(username)[0];
oform.onsubmit = function(){
    if(username.value == ""){
        alert("用户名不能为空！");
        document.forml.uscrname.focus();//获取焦点
        event.preventDefault();//阻止默认提交
    }
    console.log("密码是："+document["form1"]["pwd"]["value"]):
}
oa.addEventListener("click",function(e){
    //event.preventDefault();/阻止默认链接-方法1
    e.preventDefault();
    e.returnValue=false ;//阻比默认链接-方法2
}
</script>


## 4、DOM0 和 DOM2 的区别

DOM0 如果你写了多个事件，只应用最后一个！

Dom2 如果您写了多个事件，它会都应用。



# 三、鼠标事件 


鼠标事件指与鼠标相关的事件，具体的事件主要有以下10个事件：

- (1)  click：按下鼠标时触发
- (2)  dblclick：在同一个元素上双击鼠标时触发
- (3)  mousedown：按下鼠标键时触发
- (4)  mouseup：释放按下的鼠标键时触发
- (5)  mousemove：当鼠标在节点内部移动时触发。当鼠标持续移动时，该事件会连触发。
- (6)  mouseenter：鼠标进入一个节点时触发，进入子节点不会触发这个事件
- (7)  mouseleave：鼠标离开一个节点时触发，离开父节点不会触发这个事件
- (8)  mouseover：鼠标进入一个节点时触发，进入子节点会再一次触发这个事件
- (9)  mouseout：鼠标离开一个节点时触发，离开父节点也会触发这个事件
- (10)  wheel：滚动鼠标的滚轮时触发

```html
<style>
    .box1{ width: 100px; height: 100px; background-color: rebeccapurple;}
    .box2{ width: 100px; height: 100px; background-color: green;}
    .box3{ width: 100px; height: 100px; background-color: blue;}
    .box4{ width: 100px; height: 100px; background-color: blanchedalmond;}
    .box5{ width: 100px; height: 100px; background-color: orange;}
</style>
<button id="btn1">鼠标单击</button>
<button id="btn2">鼠标双击</button>
<button id="btn3">鼠标按下</button>
<button id="btn4">鼠标抬起</button>

<button id="btn5" class="box1">mousemove</button>
<button id="btn6" class="box2">mouseenter</button>
<button id="btn7" class="box3">mouseleave</button>
<button id="btn8" class="box4">mouseover</button>
<button id="btn9" class="box5">mouseout</button>
<script>
var btn1 = document.getElementById("btn1");
var btn2 = document.getElementById("btn2");
var btn3 = document.getElementById("btn3");
var btn4 = document.getElementById("btn4");

btn1.onclick = function(){
    console.log("单击事件");
}
btn2.ondblclick = function(){
    console.log("双击事件");
}
btn3.onmousedown = function(){
    console.log("鼠标按下");
}
btn4.onmouseup = function(){
    console.log("鼠标抬起");
}
btn5.onmousemove = function(){
    console.log("鼠标移动了");
}
btn6.onmouseenter = function(){
    console.log("鼠标enter进入了");
}
btn7.onmouseleave = function(){
    console.log("鼠标leave离开了");
}
btn8.mouseover = function(){
    console.log("鼠标over进入了");
}
btn9.mouseout = function(){
    console.log("鼠标out离开了");
}
</sctipt>
```