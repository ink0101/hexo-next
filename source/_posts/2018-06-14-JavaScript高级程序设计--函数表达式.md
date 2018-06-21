---
title: 《JavaScript 高级程序设计》-- 函数表达式
date: 2018-06-14 14:19:11
tags:
	- JavaScript
	- 学习笔记
categories: 前端
---

### 函数表达式特征

#### 函数表达式和函数声明的区别

	定义函数的两种方式：函数表达式、函数声明。

* 函数声明： 使用function关键字 + 函数名字 

	``` js
	function name(arg1, arg2, arg3)
	```
	* 重要特征： 函数声明提升，这意味着可以把函数声明放在调用它的语句后面。
	* 可以有条件声明： 但是浏览器会认为是无条件声明，因此不要使用。
	
* 函数表达式：创建一个匿名函数赋值给一个变量。
	* 匿名函数： 没有名字的函数
	* 函数表达式和其他表达式一样，使用前必须赋值
	* 可以为变量赋值，也可以作为函数返回值
	
* ✨ 理解区别的关键是理解函数提升

<!-- more -->

### 使用函数表达式实现递归
	递归，调用自身。

* ❌ arguments.callee: 指向正在执行的函数的指针，可以用来实现对函数的递归调用，但是严格模式不能使用。

*  ✨使用函数表达式实现递归

	``` js
	var test = (function f(num) {
		if (num <= 1) {
			return 1;
		} else {
			return num * f(num - 1);
		}
	});
	```

### 闭包
	当你在一个函数内部创建一个函数的时候就产生了闭包。
	闭包是指有权访问另一个函数作用域中变量的函数。
	闭包会携带包含它的函数的作用域，因此会比其他函数占用更多的内存。

#### 区分匿名函数和闭包

[浅谈匿名函数和闭包](https://www.jianshu.com/p/0a3150afb7ed)

#### 作用域链以及作用域链的作用对理解闭包至关重要

* 执行环境
	* 变量对象
* 作用域链
	* 副作用： 闭包只能取得包含函数中任何变量的最后一个值。
	
		``` js
		function test() {
			var result = new Array();
			
			for (var i = 0; i < 10; i++) {
				result[i] = function() {
					return i;
				}
			}
			
			return result;
		}
		```
	
* 活动对象
	* this
	* arguments

#### 闭包和变量

#### 关于this对象

this对象是在运行时基于函数的执行环境绑定的。

匿名函数的执行环境具有全局性，因此this对象通常指向window。

将外部作用域中的this对象保存在一个闭包能够访问到的变量中，就可以让闭包访问该对象。

``` js
var name = "window";

var obj = {
	name: "obj",
	
	getName: function() {
		var that = this;
		return function () {
			return that.name;
		}
	}
}

obj.getName()(); // obj

```

this 对象和 arguments 也存在同样的问题。

#### 内存泄露

由于垃圾回收机制不同：

* 引用计数
* 标记-清除

引用计数会有循环引用的问题，导致内存泄露。

闭包在IE的这些版本中会导致一些特殊的问题，如果闭包的作用域链中保存着一个HTML元素，那么就意味着该元素无法被销毁。

### 模仿块级作用域

``` js
function test(count) {
	(function () {
		for ( var i = 0; i<count; i++) {
			alert(i);
		}
	})();
	
	alert(i);
}
```

这种技术经常在全局作用域中被用在函数外部，从而限制向全局作用域中添加过多的变量和函数。从而避免命名冲突，方便维护。

### 私有变量

任何在函数中定义的遍历都可以认为是私有变量。

包括： 函数的参数、局部变量、在函数内部定义的其他函数。

**闭包可以通过自己的作用域链来访问这些私有变量。**

利用这一点，创建用于访问私有变量的公有方法**（特权方法）**：

* 在构造函数中定义特权方法
	
	``` js
	function obj () {
	 	var num = 10;
	 	funciton test() {
	 		return false;
	 	}
	 	
	 	this.public = function () {
	 		num ++;
	 		return test();
	 	}
	}
	```
	
	* 缺点：必须使用构造函数模式
	
*  在私有作用域中定义私有变量或函数
	
	``` js
	(function() {
		var name = '';
		Person = function (value) {
			name = value;
		};
		Person.prototype.getName = function() {
			return name;
		}
		Person.prototype.setName = function (value) {
			name = value;
		}
	})();
	
	var person1 = new Person('a');
	console.log(person1.getName());
	person1.setName('b');
	console.log(person1.getName());
	
	var person2 = new Person('c');
	console.log(person1.getName());
	console.log(person2.getName());
	```
	
	* 缺点：每个实例都没有自己的私有变量
	
*  区别
	* 私有变量和函数式实例共享的	
	
注意： 多查作用域链中的一个层次，就会在一定程度上影响查找速度。

以上是为 自定义类型创建私有变量和特权方法，**模块模式**则是为单例创建私有变量和特权方法。

### 模块模式

单例： 只有一个实例的对象。

JavaScript 通过对象字面量创建单例。

模块模式通过为单例添加私有变量和特权方法来增强单例。

套路：

``` js
var test =  function () {
	// 定义私有变量、函数
	var a = 10;
	
	// 返回特权方法和属性
	// 公共接口
	return {
		...
	}
}

```

用处：单例初始化，维护单例私有变量。

如果必须创建一个对象，并且以某些数据进行初始化，同时还要公开一些能够访问这些私有数据的方法，那么就用模块模式吧。

### 增强模块模式

在返回对象之前加入增强代码，适合单例必须是某种类型的实例，同时还必须添加某些属性和方法对其加以增强。

套路：

``` js
var test = function () {
	// 定义私有变量、私有函数
	var num = 1;
	function method() {
		return false;
	}
	
	// 创建要返回的对象， 必须是 Xxx 类型的实例
	var obj = new Xxx();
	
	// 添加特权方法，增强这个对象
	obj.getNum = function () {
		return num;
	}
	
	// 返回这个副本
	return obj;
}
```



	
	



