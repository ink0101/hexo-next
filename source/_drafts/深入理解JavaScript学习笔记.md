---
title: 深入理解JavaScript学习笔记
tags:
	- JavaScript
	- 学习笔记
categories: 前端
---
# 深入理解JavaScript

## 原型和原型链

### 三个属性

1. `prototype` - 构造函数的属性，指向原型对象
    
2. `__proto__` - 对象的属性，指向原型对象

3. `constructor` - 原型对象的属性，指向构造函数

### 原型链图解
![prototype5](prototype5.png)


## 词法作用域和动态作用域

JavaScript中采用的是*静态作用域*，与之相对的是动态作用域。

``` javascript
var scope = "global scope";
function checkscope(){
    var scope = "local scope";
    function f(){
        return scope;
    }
    return f();
}
checkscope();
```

``` javascript
var scope = "global scope";
function checkscope(){
    var scope = "local scope";
    function f(){
        return scope;
    }
    return f;
}
checkscope()();
```
比较以上两段代码的返回结果，都是 “local scope”。

不同之处：执行上下文栈的变化不同。参考下节。


**理解闭包的关键是理解JavaScript的作用域。**

## 执行上下文栈

### 顺序执行？

> 在JavaScript中分段分析代码并执行，那么如何分段？


### 可执行代码

* 全局代码
* 函数代码
* eval代码

> 遇到一段 **可执行代码** 先做“准备工作”，即生成 **执行上下文**。

### 执行上下文

* 全局执行上下文
* 函数执行上下文

执行上下文的重要属性：

* 变量对象
* 作用域链
* this

**注意：区分全局作用域和函数作用域，这是两个完全不同的概念。**

### 执行上下文栈（ECS）

> 管理执行上下文

![执行上下文](Execution_context.png)

（图片来源网络，侵删）

图上可以看到 执行上下文栈 的行为 的大概流程。

> 当执行一段 **可执行代码** 的时候，就会创建一个执行上下文，并且压入执行上下文栈，当函数执行完毕的时候，就会将函数的执行上下文从栈中弹出。

```javascript
<!--上节两段代码分析-->
<!--我们假设ECStask是一个数组-->
const ECStask = [];

<!--第一段代码的执行上下文栈过程-->
ECStack.push(<checkscope> functionContext);
ECStack.push(<f> functionContext);
ECStack.pop();
ECStack.pop();

<!--第二段代码的执行上下文栈过程-->
ECStack.push(<checkscope> functionContext);
ECStack.pop();
ECStack.push(<f> functionContext);
ECStack.pop();
```

参考：[执行上下文详细图解](https://segmentfault.com/a/1190000012646203)

## 变量对象

### 什么是变量对象？
与执行上下文相关的数据作用域，存储在上下文中定义的变量和函数声明。

### 变量对象分情况讨论

了解全局对象：

* 全局对象


#### 全局上下文的变量对象
全局上下文中的变量对象就是全局对象。

#### 函数上下文的变量对象
活动对象表示函数上下文的变量对象。

活动对象是在进入函数上下文时刻被创建的，它通过函数的 arguments 属性初始化。arguments 属性值是 _Arguments_ 对象。

活动对象和变量对象其实是一个东西，只是变量对象是规范上的或者说是引擎实现上的，不可在 JavaScript 环境中访问，只有到当进入一个执行上下文中，这个执行上下文的变量对象才会被激活，所以才叫 activation object 呐，而只有被激活的变量对象，也就是活动对象上的各种属性才能被访问。

### 执行过程

分两个阶段处理：
1. 进入执行上下文 - 添加变量对象属性值
    * 函数的所有形参（函数上下文）
    * 函数声明（先处理函数声明，再处理变量声明）
    * 变量声明
    
    以上三个部分组成变量对象。
    
2. 代码执行 - 修改变量对象属性值

    代码执行阶段，会顺序执行代码，根据代码，修改变量对象的值。

#### 思考这两段代码的不同

``` javascript
function foo() {
    console.log(a);
    a = 1;
}

foo(); // Uncaught ReferenceError: a is not defined

function bar() {
    a = 1;
    console.log(a);
}
bar(); // 1
```

## 作用域链

> 作用域链，是由当前环境与上层环境的一系列变量对象组成，它保证了当前执行环境对符合访问权限的变量和函数的有序访问。

执行上下文的一个重要属性之一（变量对象、作用域链、this）。

变量的查找，就是在作用域链上查找。

### 作用域链是如何创建和变化的

#### 函数创建
