---
layout: post
title: 曾经最怕看到的 RequireJS 
tags: [Front-End]
---

**摘要：**记得上学那会儿在首信实习做 Java Web 时项目中用过一个叫 [Dojo](http://dojotoolkit.org/) 的前端框架，当时自己对前端完全没有概念，“美工”和“切图”等名词还时时传进耳中。那时只是稍微懂点儿 js，直到有一天看到项目中的 js 文件里有一堆 require 和 define 开头的语句，完全不明白那都是些什么操作，却很傻X地犯懒根本没有查清楚过，后来居然也浑浑噩噩、照猫画虎地一直做完了整个需求。讲真，从那之后只要看到别人的 js 代码中含有 require 和 define 等字样就心里发怵。直到现在做了前端，开始了解了前端模块化的概念，现在是时候把这一块知识搞清楚了，至少要对此有个基本的认知，不能再那么朦朦胧胧，傻傻不分。
{:.message}

## What is RequireJS?

先来看看 <a href="http://requirejs.org/" target="_blank">官方文档</a> 中的介绍：
> RequireJS 是一个 JavaScript 模块加载器。它非常适合在浏览器中使用，但它也可以用在其他脚本环境，就像 Rhino and Node。使用 RequireJS 加载模块化脚本将提高代码的加载速度和质量。

## Why RequireJS?

因为前端需要模块化。根据加载时机和方式的不同，前端模块化有以下几种类型：

### `<script>` 标签类型

最常见的类型，但是缺点非常明显：全局作用域下造成变量冲突；文件加载顺序至关重要；模块与模块之间的依赖难以解决；在大型项目中难以维护和管理。

### 同步加载

典型的就是 CommonJS 规范。多用在服务器端，最好的实现就是 Node.js。

### 异步加载

#### AMD

一种基于模块的异步加载 JavaScript 代码的机制，它推荐开发人员将 js 代码封装进一个个模块，对全局对象的依赖变成了对其他模块的依赖，无须再声明一大堆的全局变量。通过延迟和按需加载来解决各个模块的依赖关系。模块化的 js 代码好处很明显，各个功能组件的松耦合性可以极大提升代码的复用性、可维护性。这种非阻塞式的并发式快速加载，使页面上其他不依赖 js 的 UI 元素，如图片、CSS 以及其他 DOM 节点得以先加载完毕，页面加载速度更快，用户也得到更好的体验。**RequireJS 是 AMD 规范的最好实现。**

* **异步加载：**通常网站都会把 script 标签的放在 html 的最后，这样就可以避免浏览器执行 js 带来的页面阻塞。使用 RequireJS，会在相关的 js 加载后执行回调函数，这个过程是异步的，所以它不会阻塞页面，避免网页失去响应。

* **按需加载：**通过 RequireJS 可以在需要加载 js 逻辑的时候再加载对应的 js 模块，这样避免了在初始化网页的时候发生大量的请求和数据传输。

* **依赖管理：**通过 RequireJS 可以避免因为 script 标签顺序问题从而导致依赖关系发生的错误。这样可以确保在所有的依赖模块都加载以后再执行相关的文件，从而起到依赖管理的作用。

* **版本管理：**如果使用 script 标签的方式引入一个 jQuery2.x 的文件，当需要换成 jQuery3.x 时就不得不修改所有页面。但是如果有在 RequireJS 的 config 中做 jQuery 的 path 映射，那只需要改一处地方即可。

#### CMD

CMD 规范是国内发展出来的，就像 AMD 有个 RequireJS，CMD 有个 SeaJS，SeaJS 要解决的问题和  RequireJS 一样，只不过在模块定义方式和模块加载（可以说运行、解析）的时机上有所不同。

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

## AMD 与 CMD 的区别

AMD 和 CMD 最大的区别是对依赖模块的执行时机处理不同，**注意不是加载的时机或者方式不同。**它们都是异步加载。

1. AMD 推崇依赖前置，在定义模块的时候就要声明其依赖的模块 
2. CMD 推崇就近依赖，只有在用到某个模块的时候再去 require

AMD 依赖前置，js 可以方便知道依赖模块是谁，立即加载。在加载模块完成后就会执行该模块，所有模块都加载执行完后会进入 require 的回调函数，执行主逻辑，这样的效果就是依赖模块的执行顺序和书写顺序不一定一致，看网络速度，哪个先下载下来，哪个先执行。但是主逻辑一定在所有依赖加载完成后才执行。

CMD 就近依赖，需要把模块变为字符串解析一遍才知道依赖了哪些模块。加载完某个依赖模块后并不执行，只是下载而已，在所有依赖模块加载完成后进入主逻辑，遇到 require 语句的时候才执行对应的模块，这样模块的执行顺序和书写顺序是完全一致的。

换句话说，AMD 是预加载，在并行加载 js 文件的同时还会解析执行该模块（因为还需要执行，所以在加载某个模块前，这个模块的依赖模块需要先加载完成）；而 CMD 是懒加载，虽然会一开始就并行加载 js 文件，但是不会执行，而是在需要的时候才执行。

AMD 优点：加载快速，尤其遇到多个大文件，因为并行解析，所以同一时间可以解析多个文件。
AMD 缺点：并行加载，异步处理，加载顺序不一定，可能会造成一些困扰，甚至为程序埋下大坑。

CMD 优点：因为只有在使用的时候才会解析执行 js 文件。因此，每个 js 文件的执行顺序在代码中是有体现的，是可控的。
CMD 缺点：执行等待时间会叠加。因为每个文件执行时是同步执行（串行执行），因此时间是所有文件解析执行时间之和，尤其在文件较多较大时，这种缺点尤为明显。

这也是很多人说 AMD 用户体验好，因为没有延迟，依赖模块提前执行了；而 CMD 性能好，因为只有用户需要的时候才执行的原因。