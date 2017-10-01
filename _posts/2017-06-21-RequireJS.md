---
layout: post
title: 'RequireJS'
tags: [Front-End]
description: >
  这里写摘要。
---

**NOTE**: 这里也可以写摘要。
{:.message}

## What is RequireJS?

先来看看 <a href="http://requirejs.org/" target="_blank">官方文档</a> 中的介绍：
> RequireJS 是一个 JavaScript 模块加载器。它非常适合在浏览器中使用，但它也可以用在其他脚本环境，就像 Rhino and Node。使用 RequireJS 加载模块化脚本将提高代码的加载速度和质量。

## Why RequireJS?

**AMD：**一种基于模块的异步加载 JavaScript 代码的机制，它推荐开发人员将 js 代码封装进一个个模块，对全局对象的依赖变成了对其他模块的依赖，无须再声明一大堆的全局变量。通过延迟和按需加载来解决各个模块的依赖关系。模块化的 js 代码好处很明显，各个功能组件的松耦合性可以极大提升代码的复用性、可维护性。这种非阻塞式的并发式快速加载，使页面上其他不依赖 js 的 UI 元素，如图片、CSS 以及其他 DOM 节点得以先加载完毕，页面加载速度更快，用户也得到更好的体验。**RequireJS 是 AMD 规范的最好实现。**

* **异步加载：**通常网站都会把 script 标签的放在 html 的最后，这样就可以避免浏览器执行 js 带来的页面阻塞。使用 RequireJS，会在相关的 js 加载后执行回调函数，这个过程是异步的，所以它不会阻塞页面，避免网页失去响应。

* **按需加载：**通过 RequireJS 可以在需要加载 js 逻辑的时候再加载对应的 js 模块，这样避免了在初始化网页的时候发生大量的请求和数据传输。

* **依赖管理：**通过 RequireJS 可以避免因为 script 标签顺序问题从而导致依赖关系发生的错误。这样可以确保在所有的依赖模块都加载以后再执行相关的文件，从而起到依赖管理的作用。

* **版本管理：**如果使用 script 标签的方式引入一个 jQuery2.x 的文件，当需要换成 jQuery3.x 时就不得不修改所有页面。但是如果有在 RequireJS 的 config 中做 jQuery 的 path 映射，那只需要改一处地方即可。

## How to use RequireJS?

只需要在页面中引入一个 require.js 即可：

```html
<script src="js/lib/require.js" data-main="js/src/main" defer async="true"></script>
```

加载 require.js 脚本的 script 标签加入了 data-main 属性，这个属性指定的 js 将在加载完 require.js 后处理。当把 require.config 的全局配置加入到 data-main 后，就可以使每一个页面都使用这个配置，然后页面中就可以直接使用 require 来加载所有的模块。

### 全局配置 require.config()

首先明确一下 RequireJS 中路径的规则：

1. 如果 script 标签引入 require.js 时没有指定 **data-main** 属性，则以引入该 js 的 html 文件所在的路径为根路径。

2. 如果有指定 **data-main** 属性，也就是有指定入口的文件，则以入口文件所在的路径为根路径。一般在该入口文件中配置 require.config()。

3. 如果在 require.config() 中有配置 **baseUrl**，则以 **baseUrl** 的路径为根路径。

以上三条优先级逐级提升，如果有重叠，后面的根路径覆盖前面的根路径。

* **baseUrl:** 定义加载模块的根路径。

* **paths:** 用来配置模块加载的路径，其实就是给模块起一个更短更好记的名字，比如将 Google 的 jQuery CDN 地址标记为 jquery，这样在 require 时只需要写 `["jquery"]` 就可以加载该 js。paths 还可以配置多个路径，比如如果远程的 CDN 库没有加载成功，那么加载本地的库。**加载模块时不用写  `.js` 后缀。**

* **shim:** 通过 require 加载的模块一般都需要符合 AMD 规范，即使用 define 来申明模块，但是部分时候需要加载非 AMD 规范的 js 以及 插件，这时候就需要用到 shim 将非标准的 AMD 模块“垫”成可用的模块。

```js
//   js/src/main.js
require.config({
    baseUrl : "js",
    paths : {
        "jquery" : ["https://ajax.googleapis.com/ajax/libs/jquery/3.2.1/jquery.min", "lib/jquery"],
        "amd" : "src/amd",
        "unamd1" : "src/unamd1"
    },
    shim: {
        "unamd1" : {
            exports : "_"
        },
        "unamd2" : {
            deps: ['jquery', 'amd'],
            exports: '~'
        },
        'jquery.scroll': {
            deps: ["jquery"],
            exports: "jQuery.fn.scroll"
        }
    }
});
```

### 基本 API

require.js 整个源文件包括注释只有 2000 来行，其对外暴露的变量其实也只是三个：

* **require:** 使用 **`require = function (array, callback)`** 来加载依赖模块，并执行加载完后的回调。require API 接受的第一个参数是所依赖模块的一个数组。即使只需要一个依赖，也需要把这个依赖放进数组中传入。第二个参数是回调的 function，用来处理加载完毕后的逻辑。回调方法中的参数就是所依赖模块的输出变量。如果某个模块不输出变量值或者不需要在回调方法中用到，则可以不用填写。所以尽量将有输出的模块写在前面以防止位置错乱引发误解。

* **define:** 使用 **`define = function (name, deps, callback)`** 来申明定义模块。define API 接受的第一个参数是定义的模块名，第二个参数是传入定义模块所需要的依赖，第三个参数则是定义模块的主函数，主函数和 require 的回调函数一样，同样是在依赖加载完成以后再调用执行。

```js
//   js/src/data.js
define(function(){
    return {
        "note" : "这个 js 模块仅仅返回一个 json 对象",
    };
});

//   js/src/alert.js
define(['src/data'], function(result){
    return function (){
        alert(result.note);
    };
});

//   js/src/main.js
require(["jquery", "unamd1", "src/alert", "jquery.scroll"], function($, _, iAlert){
    $(function(){
        _.each([1,2,3], alert);
        iAlert();
    })
});
```

* **requirejs:** 只是 require 的一个别名，目的是如果页面中已经有了 require 的其它实现，还是能通过使用 requirejs 来使用 RequireJS API 的。