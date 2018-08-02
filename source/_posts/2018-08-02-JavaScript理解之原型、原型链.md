---
title: JavaScript理解之原型、原型链
date: 2018-08-02 19:37:33
tags:
	- JavaScript
categories: 前端
---

## 原型

原型本质上就是一个对象。
> 我们创建的每个函数都有一个 prototype 属性，这个属性是一个指针，指向一个对象。
> 我们创建的每个对象都有一个 `__proto__` 属性，这个属性也是一个指针，指向一个对象。

举一个栗子来说明一下原型的概念：

首先，随意创建一个普通函数，这个函数有一个prototype属性，输出：

``` javascript
function foo () {
    console.log('foo 的原型对象', foo.prototype);
}

foo();
```

<!-- More -->

foo.prototype 指向的是一个对象，这个对象就是函数的**原型对象**。

输出结果： 

```javascript
{
    constructor: f foo()
    __proto__: Object
}
```
根据输出结果可以看到：

这个 **原型对象** 有一个 constructor 属性，这个属性指向 prototype 属性所在的函数 foo。

还有一个属性 `__proto__` 这个属性指向一个对象， 这个对象是 Object 的原型对象。因为函数 foo 的原型对象其实就是一个普通的对象，是 Object 的实例。

那么将函数 foo 重命名为 Foo，摇身一变，就是一个自定义的构造函数了。

> 构造函数和普通函数没什么区别，只是约定构造函数要首字母大写。

``` javascript
function Foo(name) {
    this.name = name;
}
Foo.prototype.getName = function () {
    return this.name;
}

// new 一个实例对象
const foo = new Foo('foo');

console.log(foo.__proto__ === Foo.prototype); // true
console.log(foo.name, foo.getName()); // 'foo', 'foo'
```

构造函数的 prototype 属性指向一个对象，这个对象就是原型对象。
根据构造函数new一个实例，实例对象的 `__proto__` 属性 也指向原型对象。
原型对象有一个constructor属性，这个属性指向构造函数。

如下图（图片来源MDN）：

![原型对象、构造函数、实例对象关系](prototype.jpg)


## 原型链

> 每一个实例对象都有一个属性 `__proto__`指向它的原型对象，原型对象也有自己的原型对象，层层向上直到一个对象的原型对象为 null。
> null 没有原型，是原型链的最后一个环节。

一个普通对象，既可以是实例对象，也可以是原型对象，即同时拥有`__proto__`属性和 constructor 属性，这样的对象就是构成原型链的节点。

JavaScript是基于原型的，原型链的结构为实现继承提供了理论基础。

下一篇将会继续分析JavaScript中继承的实现，敬请期待~

