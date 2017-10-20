---
layout: post
title: 关于 JavaScript 中的原型对象和原型链
tags: [Front-End]
---

**摘要：**用 `jQuery` 带来的一个弊端就是会幼稚地认为用原生 `JavaScript` 写 js 很低级，反正对我就是这样。一开始都是先学了点 js 的皮毛，后来遇到了 `jQuery` 发现原来操作 DOM 的语法竟是这般简单，于是就放弃了继续学习原生 `JavaScript`。直到看到越来越多的前端框架层出不穷，才明白了 `JavaScript` 的学习不是框架的学习，只有明白了其核心，才能对各种框架快速上手。这里就对最早前不屑而之前开始惧怕的原型链等概念做一个总结，希望能够对未来的学习有好的帮助。 
{:.message}

## 一切都是对象

当然这里是说除了 `undefined`、`null`、`boolean`、`number`、`string` 这 5 种按值访问的基本数据类型，也叫值类型。剩下的都是按引用访问的对象类型 `object`，也叫引用类型。

值类型的类型判断用 `typeof`，引用类型的类型判断用 `instanceof`。但是有以下几个特例：

```js
typeof null;  //object
typeof function() {}  //function
```

对象的定义很简单，说白了，**对象就是属性的集合。**比如，方法是对象的一种，它也有自己的属性。例如，在 `jQuery` 源码中，`jQuery` 或者 `$` 这个变量其实是一个方法，而 `$.ajax()` 也是一个方法，所以这就可以说明 trim 其实就是 `$` 的一个属性名，这个属性名对应的属性值是一个方法。

基于上述的例子，侧面印证了这样一个结论：**一切引用类型都是对象，而对象是属性的集合。**

## 方法和对象的关系

先看一个例子：

```js
    function Blog() {
        this.name = 'whatwhyhow7';
        this.year = 2016;
    }
    var blog = new Blog();
```

可以看到，对象是可以通过方法来创建的。但事实不止于此，因为：**对象都是通过方法 new 出来的。**

有人可能会反驳，这不对吧，下面的 `obj` 和 `arr` 明明不是通过方法创建的。

```js
    var obj = { a : 10, b : 20 };
    var arr = [7, 'w', true];
```

但是不好意思，其实以上代码的本质是：

```js
    var obj = new Object();
    obj.a = 10;
    obj.b = 20;

    var arr = new Array();
    arr[0] = 7;
    arr[1] = 'w';
    arr[2] = true;
```

而其中的 `Object` 和 `Array` 都是方法。

至此，我们发现，对象是由方法创建的，而方法却又是一种对象。它不像数组一样简单的只是对象的子集，方法和对象的关系简直就是鸡生了蛋，蛋又生了鸡。

## 原型 prototype

前面说了方法是对象，对象就有属性。`JavaScript` 就给每个方法都设置了一个默认的属性：`prototype`。同时，这个 `prototype` 的属性值也是一个对象，这个对象还有一个默认的属性：`constructor`，指向这个方法本身。

既然这个 `prototype` 是一个对象，那我们就可以自定义增加许多属性。通过自定义属性，那么由一个方法创建的对象就拥有了这个方法原型 `prototype` 上的的所有属性。

同时，`JavaScript` 还为每个对象都设置了一个默认的隐藏属性 `__proto__`，这个属性引用了创建这个对象的方法的 `prototype`。即：`blog.__proto__` === `Blog.prototype`。

## 隐式原型 `__proto__`

