---
layout: post
title: JavaScript 中的原型对象和原型链
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

## 原型 `prototype`

前面说了方法是对象，对象就有属性。`JavaScript` 就给每个方法都设置了一个默认的属性：`prototype`。同时，这个 `prototype` 的属性值也是一个对象，这个对象还有一个默认的属性：`constructor`，指向这个方法本身。

既然这个 `prototype` 是一个对象，那我们就可以自定义增加许多属性。通过自定义属性，那么由一个方法创建的对象就拥有了这个方法原型 `prototype` 上的的所有属性。

同时，**`JavaScript` 还为每个对象都设置了一个默认的隐藏属性 `__proto__`，这个属性指向创建该对象的方法的 `prototype`**。即：`blog.__proto__ === Blog.prototype`。

## 隐式原型 `__proto__`

既然方法的 `prototype` 也是对象，这个对象本质上就和 `var obj = {}` 一样都是被 `Object` 创建的，所以它的 `__proto__` 指向的就是 `Object.prototype`。

但是， `Object.prototype` 是一个特例：它的 `__proto__` 指向的是 `null`，即 `Object.prototype.__proto__ === null`。

另一方面由于对象都是由方法创建的，也就是说包括自定义方法在内的所有对象都是由方法 `Function` 创建的。所以 `Object.__proto__ === Function.prototype`。

同时，`Function` 也是一个方法和一种对象，所以 `Function` 是被自身创建的，即 `Function.__proto__ === Function.prototype`，`Function.prototype.__proto__ === Object.prototype`。

## 运算符 `instanceof`

`instanceof` 运算符其实表示的是一种继承关系，或者原型链的结构。它的第一个变量是一个对象，暂时称为 A；第二个变量一般是一个方法，暂时称为 B。它的判断规则是：同时沿着 A 的 `__proto__` 和 B 的 `prototype` 来找，如果两条线能找到同一个引用对象，那么就返回 `true`。如果找到终点还未重合，则返回 `false`。

以下是原型链结构的图示说明，可以反复对照方便理解和记忆：

![原型链](/blog/assets/img/docs/JavaScript-Prototype/01.png)

## 原型链

**访问一个对象的属性时，先在其基本属性中查找，如果没有，再沿着 `__proto__` 这条链向上找，这就是原型链。**

那么我们在实际应用中如何区分一个属性到底是基本的还是从原型中找到的呢？答案就是：`hasOwnProperty`，特别是在 `for…in…` 循环中一定要注意。

这个 `hasOwnProperty` 方法是从 `Object.prototype` 中来的。由于所有对象的原型链都会找到 `Object.prototype`，因此所有对象都会有 `Object.prototype` 里的方法。这就是所谓的"继承"。