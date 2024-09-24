---
layout: default
title: Js Dom Node
parent: JavaScript
grand_parent: Front-end Dev
nav_order: 15
---

 Here are JavaScript examples .
{: .fs-6 .fw-300 }

1. TOC
{:toc}


# 一、Node(节点)概述

DOM 是文档对象模型的简称。它的基本思想是：

把结构化文档解析成一系列的节点，再由这些节点组成一个树状结构（DOM Tree）。

所有的节点和最终的树状结构，都有规范的对外接口，以达到使用编程语言操作文档的目的（比如增删内容）。


## 1、什么是节点 Node

node 是 DOM 的最小组成单位，一个文档的树形结构就是由各种不同类型的节点组成。

对于 HTML 文档，node 主要有以下六种类型：

| 节点 | 名称 | 含义 |
|------|-----|-----|
| Document | 文档node | 整个文档（window.document) |
| DocumentType | 文档类型node | 文档的类型（比如<!DOCTYPE htmI>）| 
| Element  | 元素node  |  HTML元素（比如`<body>`、`<a>`等)| 
| Attribute | 属性node |  HTML元素的属性（比如class="right"）| 
| Text | 文本node | HTML文档中出现的文本 | 
|  DocumentFragment | 文档碎片node | 文档的片段 | 


## 2、Node 节点与节点属性

（1）通用属性

- nodeName：获取节点名称
- nodeType：获取节点类型

常见的名称和类型

| 类型 | nodeName | nodeType |
|------|---------|----------|
| DOCUMENT_NODE | #document | 9 |
| ELEMENT_NODE | 大写的HTML元素名 |1 |
| ATTRIBUTE_NODE | 等同于Attr.name| 2|
| TEXT_NODE | #ext | 3|
|DOCUMENT_FRAGMENT_NODE | #document-fragment| 11|
|DOCUMENT_TYPE_NODE| 等同于DocumentType.name|10|


（2） 获取 node 的相关节点-兄弟节点

- ownerDocument 返回当前节点元素的顶层对象-document
- nextSibling 获取下一个兄弟节点元素，包括文本节点和元素节点
- nextElementSibling 获取下一个兄弟节点元素，会跳过所有的非元素节点（如文本节点和注释节点）
- previousSibling 获取上一个兄弟节点元素，包括文本节点和元素节点
- previousElementSibling：获取下一个兄弟节点元素，会跳过所有的非元素节点（如文本节点和注释节点）


nextSibling 和 nextElementSibling 的区别

- nextSibling 属性返回当前元素在其父元素的子节点列表中的下一个节点。这个“下一个”是基于节点在DOM树中的物理位置来确定的，而不是基于文档流（即，它不考虑节点是否为元素节点）。因此，nextSibling 可能会返回一个文本节点（如果当前元素的后面紧跟着一个文本节点），或者如果当前元素是最后一个子元素，它可能会返回 null。

- nextElementSibling 属性则只返回当前元素在其父元素的子元素列表中的下一个元素节点。与 nextSibling 不同，nextElementSibling 会跳过所有非元素节点（如文本节点和注释节点），只返回下一个元素节点。如果没有下一个元素节点，它将返回 null。

> previousSibling 和 previousElementSibling 同理。

```html
<div id="parent">
	<span id="first">First</span>
	<!-- This is a comment -->
	<span id="second">Second</span>
</div>
<script>
    var firstSpan = document.getElementById('first');
	var nextSibling = firstSpan.nextSibling; // 可能是注释节点，或者在某些浏览器中可能是文本节点（空白字符）
	var nextElementSibling = firstSpan.nextElementSibling; // 是id为'second'的<span>元素
	
	console.log(nextSibling); // 输出可能是注释节点或者null（取决于浏览器如何处理空白字符）
	console.log(nextElementSibling); // 输出是<span id="second">Second</span>
</script>
```

> 由于 nextElementSibling 只会返回元素节点，因此当你知道你想要的是下一个兄弟元素时，使用它会更加方便和直观。然而，如果你需要处理所有类型的节点（包括文本节点和注释节点），那么你应该使用 nextSibling。但请注意，在使用 nextSibling 时，你可能需要添加额外的检查来确保你正在处理的是期望的节点类型。


（3）node 的相关节点-父节点

- parentNode：获取父节点元素
- parentElement：获取父节点元素


（4）node 内容属性

- innerHTML：设置或者是返回 html 标签直接的内容
- innerText：设置或者返回文本内容。HTML 标签不能解析，会原样输出。
- textContent：设置或者返回文本内容。
- nodeValue：返回节点的值

```html
<div id="div1">这是第1个块元素</div>
<div id="div2">这是第2个块元素</div>
<div id="div3">这是第3个块元素</div>
<div id="div4">这是第4个块元素</div>
<p>这是一段文字</p>
<button>设置值</button>
<button>返回值</button>
<script>
var obt = document.querySelectorAll("button");
var divAll = document.querySelectorAll("div");
var odiv1 = document.getElementById("div1") ;
var odiv2 = document.getElementById("div2") ;
var odiv3 = document.getElementById("div3") ;
var odiv4 = document.getElementById("div4") ;
var op = document.querySelector("p");

//设置样式
obt[0].onclick = function(){
    odiv1.innerHTML = "<a href='http://doc.zhangxiaocai.cn' >NoteBook</a>";
    odiv2.innerText = "<a href='http://doc.zhangxiaocai.cn' >NoteBook InnerText</a>";
    odiv3.textContent = "<p>NoteBook textContent</p>";
    odiv4.nodeValue = "<span> zhangxiaocai nodeValue </span>";
}
obt[1].onclick = function(){
    console.log(odiv1.innerHTML);
    console.log(odiv2.innerText);
    console.log(odiv3.textContent);
    console.log(odiv4.nodeValue);
    console.log(divAll.firstChild.nodeValue);
    console.log(op.childNodes[0]);
    console.log(op.childNodes[0].childNodes[0].nodeValue);
}
</script>
```


（5）获取当前 node 子节点相关属性

- firstChild 获取第一个子节点
- lastChild 获取最后一个子节点
- firstElementChild : 获取第一个子节点, 没有空白和换行
- lastElementChild : 获取最后一个子节点, 没有空白和换行
- childNodes：获取子节点（数组）。childNodes 可以获取元素节点和文本节点。
- Children：获取子节点（数组）。children 只能获取元素节点，不能获取文本节点。


```html

<divclass="box">
    <ul>
        <h2>标题</h2>
        <li>搜狐<a href="http://www.doc.zhangxiaocai.cn">隐藏</a></li>
        <li>雅虎<a href="javascript:void(0)">隐藏</li>
        <li>腾讯<a href="javascript:void(0)">隐藏</li>
        <li>华为<a href="javascript:void(0)">隐藏</li>
        <li>百度<a href="javascript:void(0)">隐藏</li>
        <p>11</p>
        <p>22</p>
    </ul>
</div>
<button> 获取子节点 </button>
<script>
var oh2 = document.querySelector("h2") ;
var oul = document.querySelector("ul") ;
var obtn = document.querySelector("button") ;


obtn.onclick = function() {
    console.log(oul.firstChild);//这个有空白和换行节点
    console.log(oul.firstElementChild) ;//直接获取子节点，没有空白的和换行节点
    console.log(oul.lastChild.previousSibling);//获取的是#text文本节点 （换行)
    console.log(oul.childNodes[0].nextSibling);
    console.log(oul.childNodes[0].nextSibling.childNodes[0].nodeValue);
    console.log(oul.children[1]); //获取子节点，没有空白节点
}

</script>
```

# 二、Node 的方法(重点)


## 1、节点的增删改查


- appendChild()：向后添加（追加）子节点
- cloneNode()：克隆子节点
- insertBefore()：向前添加子节点
- removeChild()：删除子节点
- replaceChild()：修改子节点
- hasChildNodes():判断是否有子节点（true,false）。空白节点也是子节点。
- contains()：判断是否包含指定的子节点（true,false）。
- isEqualNode():判断两个节点是否相等


```html
<body>
<div class="box">

</div>
<button>克隆子节点</button>
</body>
<script>
var odiv = document.querySelector("div");//获取div元素
var op = document.createElement("p");//创建一个p元素
var otxt = document.createTextNode("这是一段大佬的介绍"); //创建文本
op.appendChild(otxt); //给p元素添加文本内容
odiv.appendChild(op); //将P元素添加到div元素中


var obt =document. querySelector("button");
obt.onclick = function() {
    odiv.appendChild(p.cloneNode(true));//将克隆的第二段添加到div元素中
}
console.log(p.cloneNode(true));// true: 表示所有的子节点都会
</script>
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
<!DOCTYPE html>
<html lang="en">
<head>
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
var oa = document.querySelector("a");
var oform = document.querySelector("form");
var username = document. getElementsByName(username)[0];
oform.onsubmit = function(){
    if(username.value == ""){
        alert("用户名不能为空！");
        document.forml.uscrname.focus();//获取焦点
        event.preventDefault();//阻止默认提交
    }
    console.log("密码是："+document["form1"]["pwd"]["value"]);
}
oa.addEventListener("click",function(e){
    //event.preventDefault();/阻止默认链接-方法1
    e.preventDefault();
    e.returnValue=false ;//阻比默认链接-方法2
}
</script>
</body>
</html>
``` 


## 4、DOM0 和 DOM2 的区别

DOM0 和 DOM2 的区别

- DOM0 如果你写了多个事件，只应用最后一个！

- Dom2 如果您写了多个事件，它会都应用。

[DOM0, DOM1, DOM2, DOM3事件的区别与详解](https://blog.51cto.com/knifeedge/5010718)


# 三、鼠标事件 

语法：
```
元素.on+鼠标事件名称 = 调用函数
```
例如：
```
d1.ondblclick = function () { console.log('这是 d1');}
```

鼠标事件指与鼠标相关的事件，具体的事件主要有以下10个事件：

- (1)  click：按下鼠标时触发
- (2)  dblclick：在同一个元素上双击鼠标时触发
- (3)  mousedown：按下鼠标键时触发
- (4)  mouseup：释放按下的鼠标键时触发
- (5)  mousemove：当鼠标在节点内部移动时触发。当鼠标持续移动时，该事件会连触发。
- (6)  mouseenter：鼠标进入一个节点时触发，进入子节点不会触发这个事件(不冒泡)
- (7)  mouseleave：鼠标离开一个节点时触发，离开父节点不会触发这个事件(不冒泡)
- (8)  mouseover：鼠标进入一个节点时触发，进入子节点会再一次触发这个事件(冒泡)
- (9)  mouseout：鼠标离开一个节点时触发，离开父节点也会触发这个事件(冒泡)
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
</script>                                   
```


# 四、IE 事件

## 1、事件绑定

在 js 脚本中，通过 attachEvent 函数绑定事件
语法：
```
元素.attachEvent(type,listener)
```
- type:事件类型。【有 on！有 on！有 on！】
- listener:监听函数，绑定的函数
> 注意:如果绑定多个函数，按照函数书写的倒叙执行。

## 2、事件解除

IE 下 DOM2 级事件的移除方式：
```
元素.detachEvent(type,listener)
```
ps：匿名函数无法被移除。

## 3、事件兼容

由于【IE 浏览器中的事件绑定】和【非 IE 浏览器中的事件绑定】方式方法都有所不同。所以单一的某种函数都不能完美解决不同浏览器下的方法绑定问题。

```html
<script>
//封装一个函数，让各个湖览器都能给元素添加事件
//element：元素，type：事件类型，method：函数方法
var obt = document.querySelector(button"):
function addEvent(element, tvpe,method){
    if(element. addEventListener) {
        element.addEventListener (type, method)
    } else if(element.attachEvent) {
        element.attachEvent ("on"+type, method) ;
    }else {
        element['on'+type] = method ;//obt[onclick]= function(){}
    }
}

function show(){
    alert("我是show方法!");
}

addEvent(obt,"click", show);
</script>
```


# 五、表单事件

## （1）表单文档事件

焦点：js 当前正在和用户发生交互的节点称为焦点。可以类比为人类目光汇聚的地方。

语法：获得焦点和失去焦点事件既可以使用 DOM1 绑定也可以使用 DOM2 绑定。

>获得焦点和失去焦点事件均不支持事件冒泡。


```html
<body>
<form action="">
用户名：<input type="text" class="username"/><br>
手机：<input type="text"/><br>
<input type="submit" value="提交"/>
</form>
<script>
var username = document.querySelector(".username");
//onfocus：获取焦点事件
username.onfocus = function(){
    this. style.cssText = "border:1px solid yellow;background:grey:"
}
//onblur：失去焦点事件
username.onblur = function(){
    this. style.cssText = "";
}
</script>
</body>
```

## （2）oninput 事件和 onchange 事件

onchange事件: 元素发生变化的时候，就会触发。

oninput事件: 当给元素输入内容的时候，就会触发。


区别：
- onchange:当失去焦点的时候才会触发此事件。
- oninput:当输入内容的时候，会立即触发。



```html
<style>
.userfalse{ color: red; }
.usertrue{ color: green;}
</style>
<body>
<form action="">
用户名：<input type="text” class="username"/><span class="usershow"></span><br>
手机：<input type="text"/><br>
<input type="submit" value="提交"/>
</form>
<script>
var username = document.querySelector(".username");
var usershow = document.querySelector(".usershow");
//oninput：内容变化
username.oninput = function(){
   console.log(" oninput 事件")
    if(username.value.length <5 || username.value.length>20){
        usershow.innerHTML ="错误，请输入5-20个字符！";
        //usershow.className = "userfalse";
        usershow.setAttribute("class","userfalse");
    }
    else {
        usershow.innerHTML ="正确";
        usershow.setAttribute("class","usertrue");
    }
    console.log("触发了oninput事件!");
}

//onchange：元素发生变化触发此事件
username.onchange = function(){
   console.log(" onchange 事件")
}
</script>
</body>
```

# 六、键盘事件

键盘事件是指当用户在操作键盘的时候会自动被触发的事件。

通常有以下三种：

- (1) onkeydown:用户按下任意键都可以触发这个事件。如果按住不放，事件会被连续触发。

- (2) onkeypress:用户按下任意键都可以触发这个事件(功能键除外)。如果按住不放，事件会被连续触发。

- (3) onkeyup: 用户释放按键时触发。

> ： 键盘事件一般绑定在需要用户输入的元素上( 例如 input)，但是由于键盘事件默认采用事件冒泡机制，因此将键盘事件直接绑定在 body 之上也是允许的。

**键盘属性**

key 和 keyCode 属性

- Key:具体是哪一个键
- keyCode:返回 keydown 何 keyup 事件发生的时候按键的代码，以及
- keypress 事件的 Unicode 字符(ascii 码值)。 如: A:65,a:97,0:48,空格键：32.

```html
<style>
.box{ width:100px; height: 100px; background: red; position: relative; left: 50%; bottom:-350px:}
@keyframes fly { 100%{ transform: translateY(200px) ; } }
</style>
<body>
<div class="box"></div>
<script>
var box = document.querySelector(". box") ;
document.onkeydown = function(e){
switch(keyCode)
	case 37:
		box.style.left = box.offsetLeft + 25+"px"; break;
	case 38:
		box.style.top = box.offsetTop +  20 +"px"; break;
	case 39:
        box.style.left = box.offsetLeft + 25+"px"; break;
	case 40:
        box.style.top = box.offsetTop + 20 +"px"; break;
	case 32:
		var bomp = document.createElement("div");
		box.appendChild(bomp);
		bomp.style.width = "10px";
		bomp.style.height = "30px";
		bomp.style.background = "green";
        bomp.style.position = "absolute";
		bomp.style.left = "46px";
		bomp.style.animation ="fly 2s";
		break;
</script>
```

# 七、滚动事件

## 回到页面顶部

(1) 锚点方式

```html
<body style="height:2000px;">
<div id="topAnchor"></div>
<a href="#topAnchor" style="position:fixed;right:0;bottom:0">回到顶部
</a>
</body>
```

示例:

```html
<html lang="en">
<head >
<meta charset="UTF-8">
<title></title>
<style>
html { height:2000px;} 
a { position:fixed; right:0; bottom:0;}
</style>
</head>
<body>
<div id="top">这是最顶端</div>
<a href="#top">回到顶部</a>
</body>
</html>
```

(2) scrollTop 属性


scrollTop 属性表示被隐藏在内容区域上方的像素数（文档距离顶部的距离）。

元素未滚动时，scrollTop 的值为 0，如果元素被垂直滚动了，scrollTop 的值大于 0，且表示元素上方不可见内容的像素宽度。

由于 scrollTop 是可写的，可以利用 scrollTop 来实现回到顶部的功能。


```html
<body style="height:2000px;">
<button id="test" style="position:fixed;right:0;bottom:0;"> 回到顶部</button>
<script>
test.onclick = function(){
    document.body.scrollTop = 0;
    document.documentElement.scrollTop = 0;
}
</script>
</body>
```


```html
<html lang="en">
<head >
<meta charset="UTF-8">
<title></title>
<style>
html { height:2000px;} 
button { position:fixed; right:0; bottom:0;}
</style>
</head>
<body>
<div id=top">这是最顶端</div>
<button>回到顶部</button>
<script>
    var obt = document.querySelector("button");
    // obt.addEventListener("click", back);
    
    obt.onclick = function()(
        back();
    }
    function back(){
        document.documentElement.scrollTop = 0;
        //document. body. scrollTop = 0;
    }
</script>
</body>
</html>
```

（3）滚动事件

onscroll：滚动事件，onscroll 事件会在【文档】或【元素】发生滚动操作时触发。

```
window.onscroll = function(){
}
```


```html
<html lang="en">
<head>
<meta charset="UTF-8">
<title></title>
<style>
html{width:2000px; height:2000px;}
</style>
</head>
<body>
<script>
window.onscroll = function(){
	console.log("onscroll被触发了！");
}
</script>
</body>
</html>
```