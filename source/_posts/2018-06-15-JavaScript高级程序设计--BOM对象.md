---
title: 《JavaScript 高级程序设计》-- BOM对象
date: 2018-06-15 13:40:06
tags:
	- JavaScript
	- 学习笔记
categories: 前端
---


ES 标准是 JavaScript 核心，但是在 Web 中使用 JavaScript，BOM（浏览器对象模型）才是真正的核心。

W3C为了将浏览器中的 JavaScript 最基本的部分标准化，已经将 BOM 的主要方面纳入了 HTML5 规范。

<!--More-->

## 总览

BOM 主要由 window 对象、location 对象、navigator 对象、screen 对象、history 对象几部分， 如下图：

![总览](BOM.png)

## window 对象

* BOM的核心对象是window对象，表示一个浏览器的实例。
* window 对象同时还是 ES 规定的 Global 对象。

主要涉及以下几个部分内容：

![window对象](window.png)

### 全局作用域

在所有全局作用域中声明的变量、函数都会变成window对象的属性和方法。前提条件是，执行环境是在浏览器中。


``` js
var age = 10;
console.log(window.age); // 10
```

Global 对象和 window 对象差别：

**全局变量不能通过delete操作符删除，但是定义在window对象上定义的属性可以通过delete删除。**

``` js
var a = 10;
<!--在 IE 9 前会报错-->
delete window.a; // false
window.b = 20;
delete window.b; // true
```
这是为什么呢？

原因： 使用 var 语言添加的 window 属性的 [[Configurable]] 的特性值为false。

全局对象是一个逻辑上的概念，是不能够直接访问的，this指向全局对象，但是在浏览器中，this的指向就是window对象。因此window对象就是全局对象在浏览器中的具体表现。

[more >> ](https://blog.csdn.net/chenchunlin526/article/details/78908592)

### 窗口关系及框架
每个框架都有自己 window 对象，并且保存在 frames 集合中。

每个 window 对象都有一个 name 属性，其中包含框架名称。

访问框架：

* 索引： `window.frames[0]`
* 框架名称： `window.frames['topFrame']`
* ✨ top 对象： `top.frames[0]` （推荐）

#### top 对象
始终指向最外层框架，即浏览器窗口。

#### parent 对象
始终指向当前框架的直接上层框架。

#### self 对象
始终指向window。

#### 框架造成instanceof判断的问题
在使用框架的情况下，浏览器会存在多个window对象。每个框架中定义的全局变量会自动成为框架中window对象的属性。由于每个window对象都包含原生类型的构造函数，因此每个框架都有一套自己的构造函数，这些构造函数一一对应，但是却不相等。因此依赖原生的构造函数来判断类型的instanceof操作符就变得不可靠了。

### 窗口位置

跨浏览器取得窗口左边和上边的位置：

``` js
var leftPos = (typeof window.screenLeft === 'number') ?
	window.screenLeft : window.screenX;
var topPos = (typeof window.screenTop === 'number') ? 
	window.screenTop : window.screenY;
```
实际上，**无法跨浏览器取得窗口左边和上边的精确位置**。因为保存的值实际含义不同。

不过可以将窗口精确的移动到一个新的位置，通过两个方法：

* moveTo（）- 接收新位置的坐标值
* moveBy（）- 接收水平和垂直方向移动的像素数

然鹅，这两个方法可能会被浏览器禁用！！！而且这两个方法不适用于框架！

### 窗口大小

* innerWidth
* innerHeight
* outerWidth
* outerHeight

跨浏览器是无法确定浏览器窗口本身的大小的，但是可以取得页面视口的大小：

``` js
var pageWidth = window.innerWidth;
var pageHeight = window.innerHeight;

if (typeof pageWidth !== 'number') {
	<!--根据document.compatMode确定页面是否处于标准模式-->
	if (document.compatMode === "CSS1Compat") {
		pageWidth = document.documentElement.clientWidth;
		pageHeight = document.documentElement.clientHeight;
	} else {
		pageWidth = document.body.clientWidth;
		pageHeight = document.body.clientHeight;
	}
}
```

Q: 可见视口和布局视口的区别？

调整浏览器窗口大小：

* resizeTo() - 接收浏览器窗口的新宽度和新高度
* resizeBy() - 接收新窗口与原窗口的宽度和高度之差

然鹅，同上，这两个方法同样可能被浏览器禁用！！！同样不适用于框架！

### 导航和打开窗口

#### window.open()

* 导航到URL
* 新打开浏览器窗口

参数：

* 要加载的URL
* 窗口目标
	* 已有窗口或框架名称
	* 特殊的窗口名称
		* _self
		* _parent
		* _top
		* _blank
* 特性字符串 - 逗号分隔的字符串，表示在新窗口中都显示那些特性
* Boolean - 表示新页面是否取代浏览器历史记录中当前加载页面的布尔值

#### 弹出窗口
第二个参数不是已经存在的窗口或框架，就根据第三个参数位置传入的字符串创建一个新窗口或新标签。

close() - 关闭新打开的窗口，仅适用于window.open（）打开的弹出窗口。

#### 安全限制
由于广告窗口太多，浏览器限制窗口打开。

#### 弹出窗口屏蔽程序
弹出窗口被屏蔽的情况：

* 浏览器内置屏蔽程序阻止的弹出窗口 - 通常返回null
* 浏览器扩展或其他程序阻止的弹出窗口 - 通常抛出错误

#### 参考：

[记一次小坑--关于window.open()](https://juejin.im/post/5af55b296fb9a07ace58ecc6)

### 间歇调用和超时调用

* setTimeout()
* setInterval()

### 系统对话框

* alert()
* confirm()
* prompt()

外观由操作系统及浏览器设置觉得，不是由CSS决定。这几个方法打开的对话框都是同步和模态的，显示这些对话框的时候代码会停止执行，关掉以后又恢复执行。

## location对象

* 保存当前窗口中加载的文档相关信息
* 导航功能，将URL解析为独立的片段

location是一个特殊的对象，既是window对象的属性，也是document对象的属性。

### 查询字符串参数

### 位置操作

* location.assign('url')
* location.href = 'url' ,  window.location = 'url'
* 修改location的hash, search, hostname, pathname, port属性
* location.replace('') - 不会生成新的历史记录
* location.reload()

## navigator 对象

识别客户端浏览器的事实标准。

### 检测插件

* 非IE
	* plugins 数组
		* name
		* description
		* filename
		* length  
* IE
	* ActiveXObject类型 - 并尝试创建一个特定插件的实例
		* COM 对象 - COM 标识符 
* 典型做法 - 针对每个插件分别创建检测函数，而不是前面介绍的通用检测方法

### 注册处理程序

* registerContentHandler()
	* 参数
		* 要处理的MIME类型
		* 可以处理改MIME类型的页面的URL
		* 应用程序的名称
* registerProtocolHandler()
	* 参数
		* 要处理的协议
		* 处理该协议的页面URL
		* 应用程序名称  

这两个方法让一个站点指明它可以处理特定类型的信息。

## screen 对象

基本上只用来表明客户端的能力。
这些信息经常集中出现在测定客户端能力的站点跟踪工具中，但是通常不会用于影响功能。

## history 对象
保存用户上网的历史记录，从窗口被打开的那一刻算起。

* go() - 在用户的历史记录中任意跳转，可以向前也可以向后
	* -1 - 向后一页
	* 1 - 向前一页
	* 字符串 - 历史记录中包含该字符串的第一个位置
* back() - 后退
* forward() - 前进
* length - 历史记录数量