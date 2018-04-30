---
layout: post
title: 题目总结
tags: [Front-End]
---

**摘要：**整理一下平时看到的一些题目，温故而知新。
{:.message}

### 如何判断某变量是否为数组数据类型？

```js
if (typeof Array.isArray === "undefined") {
    Array.isArray = function(arg) {
        return Object.prototype.toString.call(arg) === "[object Array]";
    }; 
}
```

### 什么是伪数组？如何将伪数组转化为标准数组？

伪数组（类数组）：无法直接调用数组方法或期望 length 属性有什么特殊的行为，但仍可以对真正数组遍历方法来遍历它们。典型的是函数的 arguments 参数，还有像调用 getElementsByTagName,document.childNodes 之类的,它们都返回 NodeList 对象都属于伪数组。可以使用 Array.prototype.slice.call(fakeArray) 将数组转化为真正的 Array 对象。

### trim 函数的兼容实现

```js
if (!String.prototype.trim) {
    String.prototype.trim = function() {
        return this.replace(/^\s+/, "").replace(/\s+$/, "");
    }
}
```

### clone 函数的兼容实现

```js
function clone(Obj) {
    var buf;
    if (Obj instanceof Array) {
        buf = []; //创建一个空的数组
        var i = Obj.length;
        while (i--) {
            buf[i] = clone(Obj[i]);
        }
        return buf;
    } else if (Obj instanceof Object) {
        buf = {}; //创建一个空对象
        for (var k in Obj) { //为这个对象添加新的属性
            buf[k] = clone(Obj[k]);
        }
        return buf;
    } else { //普通变量直接赋值
        return Obj;
    }
}
```

### console.log 函数的转化

```js
function log() {
    console.log.apply(console, arguments);
};
```

### 事件绑定

```js
function addEvent(elem, type, handler) {　　
    if (elem.addEventListener) {　　　　
        elem.addEventListener(type, handler, false);　　
    } else if (elem.attachEvent) {　　　　
        elem['temp' + type + handler] = handler;　　　　
        elem[type + handler] = function() {　　　　
            elem['temp' + type + handler].apply(elem);　　
        };　　
        elem.attachEvent('on' + type, elem[type + handler]);　
    } else {　　
        elem['on' + type] = handler;　　
    }
}
```

### 直接在对象的原型上添加方法是否安全？

+ 容易造成全局污染，和其他库冲突
+ 出了 Bug 不太好定位问题
+ 有可能出现代码向上不兼容的情况，比如定义了一个Object.prototype.clone。万一ES7、ES8也定义了这个函数，那旧代码就会出问题。

### JavaScript 的事件流模型都有什么？

+ 事件冒泡：事件开始由最具体的元素接受，然后逐级向上传播
+ 事件捕捉：事件由最不具体的节点先接收，然后逐级向下，一直到最具体的
+ DOM事件流：三个阶段：事件捕捉，目标阶段，事件冒泡

### 怎样添加、移除、移动、复制、创建和查找节点？

1. 创建新节点
+ createDocumentFragment() // 创建一个DOM片段
+ createElement() // 创建一个具体的元素
+ createTextNode() // 创建一个文本节点

2. 添加、移除、替换、插入
+ appendChild() // 添加
+ removeChild() // 移除
+ replaceChild() // 替换
+ insertBefore() // 插入

3. 查找
+ getElementsByTagName() // 通过标签名称
+ getElementsByName() // 通过元素的Name属性的值
+ getElementById() // 通过元素Id，唯一性

### JavaScript 实现 ajax

ajax 的原理简单来说通过 XmlHttpRequest 对象来向服务器发异步请求，从服务器获得数据，然后用 JavaScript 来操作 DOM 而更新页面。

```js
ajax({
    url: "/xxx/yyy",
    type: "POST",
    data: { name: "wwh", age: 29 },
    dataType: "json",
    success: function (text, xml) {},
    fail: function (status) {}
});

function ajax (options) {
    options = options || {};
    options.type = (options.type || "GET").toUpperCase();
    options.dataType = options.dataType || "json";

    var params = formatParams(options.data);
    var xhr = window.XMLHttpRequest ? new XMLHttpRequest() : new ActiveXObject('Microsoft.XMLHTTP');

    xhr.onreadystatechange = function () {
        if (xhr.readyState == 4) {
            var status = xhr.status;
            if (status >= 200 && status < 300) {
                options.success && options.success(xhr.responseText, xhr.responseXML);
            } else {
                options.fail && options.fail(status);
            }
        }
    }

    if (options.type == "GET") {
        xhr.open("GET", options.url + "?" + params, true);
        xhr.send(null);
    } else if (options.type == "POST") {
        xhr.open("POST", options.url, true);
        xhr.setRequestHeader("Content-Type", "application/x-www-form-urlencoded");
        xhr.send(params);
    }
}

//格式化参数
function formatParams(data) {
    var arr = [];
    for (var name in data) {
        arr.push(encodeURIComponent(name) + "=" + encodeURIComponent(data[name]));
    }
    arr.push(("v=" + Math.random()).replace("."));
    return arr.join("&");
}
```

### 使用 requestAnimationFrame 实现一个持续的动画效果

```js
window.requestAnimFrame = (function () {
    return  window.requestAnimationFrame || window.webkitRequestAnimationFrame || window.mozRequestAnimationFrame || 
            function (callback) {
                window.setTimeout(callback, 1000 / 60);
            };
})();

var button = document.getElementById("button");
var progress = 0;

function jia() {
    progress += 1;
    button.style.width = progress + "%";
    button.innerHTML=progress + "%";
    if (progress < 100) {
        requestAnimationFrame(jia);
    }
}

function jian() {
    progress -= 1;
    button.style.width = progress + "%";
    button.innerHTML=progress + "%";
    if (progress > 0) {
        requestAnimationFrame(jian);
    }
}

document.getElementById("run").addEventListener("click", function() {
    requestAnimationFrame(progress === 0 ? jia : jian);
}, false);
```

### let 与 var 的区别

+ var 声明的变量，其作用域为该语句所在的函数内，且存在变量提升现象
+ let 声明的变量，其作用域为该语句所在的代码块内，不存在变量提升
+ let 不允许重复声明

### CommonJS 中的 require/exports 和 ES6 中的 import/export 区别

+ import/export 最终都是编译为 require/exports 来执行的。
+ CommonJS 规范规定，每个模块内部，module 变量代表当前模块。这个变量是一个对象，它的 exports 属性（即 module.exports ）是对外的接口。加载某个模块，其实是加载该模块的 module.exports 属性。

### 数组去重

方法一：

```js
[...new Set([1,2,3,1,'a',1,'a'])]
```

方法二：一重循环，辅助以对象属性在不在的方式实现。

### 使用 addEventListener 实现点击 ul 中的 li 弹出其内容，并且动态添加 li 之后有效

```js
document.getElementById("ul").addEventListener('click', function (e) {
    if (e.target && e.target.nodeName.toUpperCase() == "LI") {
        alert(e.target.innerHTML);
    }
}, false);
```

### 判断两个对象相等

```js
JSON.stringify(obj1) === JSON.stringify(obj2);
```

### 模块化开发是怎么做的？

使用命名空间。

### Vue 双向绑定实现原理

通过 Object.defineProperty 实现的。

```html
<body>
    <div id="app">
        <input type="text" id="txt">
        <p id="show-txt"></p>
    </div>
    <script>
        var obj = {}
        Object.defineProperty(obj, 'txt', {
            get: function () {
                return obj
            },
            set: function (newValue) {
                document.getElementById('txt').value = newValue
                document.getElementById('show-txt').innerHTML = newValue
            }
        })
        document.addEventListener('keyup', function (e) {
            obj.txt = e.target.value
        })
    </script>
</body>
```

### Set 和 Map 两种数据结构的区别

+ ES6 提供了新的数据结构 Set 它类似于数组，但是成员的值都是唯一的，没有重复的值。
+ ES6 提供了 Map 数据结构。它类似于对象，也是键值对的集合，但是“键”的范围不限于字符串，各种类型的值（包括对象）都可以当作键。也就是说，Object 结构提供了“字符串—值”的对应，Map 结构提供了“值—值”的对应，是一种更完善的 Hash 结构实现。

### WeakMap 和 Map 的区别

+ WeakMap 与 Map 结构基本类似，唯一的区别是它只接受对象作为键名（ null 除外），不接受其他类型的值作为键名，而且键名所指向的对象，不计入垃圾回收机制。
+ WeakMap 最大的好处是可以避免内存泄漏。一个仅被 WeakMap 作为 key 而引用的对象，会被垃圾回收器回收掉。
+ WeakMap 拥有和 Map 类似的 set(key, value) 、get(key)、has(key)、delete(key) 方法, 没有任何与迭代有关的属性和方法。

### 重排和重绘

+ 部分渲染树（或者整个渲染树）需要重新分析并且节点尺寸需要重新计算。这被称为重排。注意这里至少会有一次重排-初始化页面布局。
+ 由于节点的几何属性发生改变或者由于样式发生改变，例如改变元素背景色时，屏幕上的部分内容需要更新。这样的更新被称为重绘。

### 什么情况会触发重排和重绘？

+ 添加、删除、更新 DOM 节点
+ 通过 display: none 隐藏一个 DOM 节点-触发重排和重绘
+ 通过 visibility: hidden 隐藏一个 DOM 节点-只触发重绘，因为没有几何变化
+ 移动或者给页面中的 DOM 节点添加动画
+ 添加一个样式表，调整样式属性
+ 用户行为，例如调整窗口大小，改变字号，或者滚动。

### 浏览器缓存

浏览器缓存分为强缓存和协商缓存。当客户端请求某个资源时，获取缓存的流程如下：

+ 先根据这个资源的一些 http header 判断它是否命中强缓存，如果命中，则直接从本地获取缓存资源，不会发请求到服务器；
+ 当强缓存没有命中时，客户端会发送请求到服务器，服务器通过另一些request header验证这个资源是否命中协商缓存，称为http再验证，如果命中，服务器将请求返回，但不返回资源，而是告诉客户端直接从缓存中获取，客户端收到返回后就会从缓存中获取资源；
+ 强缓存和协商缓存共同之处在于，如果命中缓存，服务器都不会返回资源；
+ 区别是，强缓存不对发送请求到服务器，但协商缓存会。
+ 当协商缓存也没命中时，服务器就会将资源发送回客户端。
+ 当 ctrl+f5 强制刷新网页时，直接从服务器加载，跳过强缓存和协商缓存；
+ 当 f5 刷新网页时，跳过强缓存，但是会检查协商缓存；

#### 强缓存

+ Expires（该字段是 http1.0 时的规范，值为一个绝对时间的 GMT 格式的时间字符串，代表缓存资源的过期时间）
+ Cache-Control:max-age（该字段是 http1.1 的规范，强缓存利用其 max-age 值来判断缓存资源的最大生命周期，它的值单位为秒）

#### 协商缓存

+ Last-Modified（值为资源最后更新时间，随服务器response返回）
+ If-Modified-Since（通过比较两个时间来判断资源在两次请求期间是否有过修改，如果没有修改，则命中协商缓存）
+ ETag（表示资源内容的唯一标识，随服务器response返回）
+ If-None-Match（服务器通过比较请求头部的If-None-Match与当前资源的ETag是否一致来判断资源是否在两次请求之间有过修改，如果没有修改，则命中协商缓存）

#### JavaScript是一门什么样的语言，它有哪些特点？

#### 你理解的前端是什么样的？

#### 遇到过哪些困难？怎么克服的？

#### 为什么换工作？有哪些收获？