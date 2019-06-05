---
layout: post
title: Babel 使用指南
tags: [Dev-Tool]
---

**摘要：**关于 Babel，一直使用得是稀里糊涂。安装了一堆包，说实话都有哪些作用和区别其实是说不上来的。再过几天就要离职了，暂时工作上没有其他事情，决定一探 Babel 的究竟。
{:.message}

## 怎么读

babel 是一个 Javascript 编译器，通俗点说，其作用就是将 Javascript 最新的语法和特性进行转换，以保证依然能够在不支持新语法和新特性的浏览器上正常运行。

同时，babel 不光支持新语法特性的转换，react，vue 的语法也是通过 babel 转换的，比如 react 项目可以使用 `preset-react`。

所以，babel 实质上是一个工具。那么这个工具应该怎么读呢？这里明确一下，正确的发音应该是：[ˈbeɪbl]，类似于“掰啵”。

## 安装

`@babel/core` 为 babel 的基础包，是必须引入的。

现在的前端项目应该都用 `webpack` 了，所以可以一起安装 `babel-loader`，并且在 `webpack` 中进行配置:

```shell
npm i @babel/core babel-loader -D
```

```js
{ 
    test: /\.js$/, 
    exclude: /node_modules/, 
    loader: 'babel-loader' 
}
```

## 配置

我们需要一个配置文件来告诉 babel 如何工作。配置的方式有 3 种：1. 在 `package.json` 中设置 babel 字段；2. `.babelrc` 文件或 `.babelrc.js`；3. `babel.config.js` 文件。

我个人倾向于使用 `.babelrc.js`，原因有 3 点：

1. 避免将 `package.json` 文件配置过长。说实话，现在一般一个项目都会安装很多依赖，`package.json` 文件已经非常臃肿了，非常不方便查阅。
2. `.js` 文件可以写注释，方便备注。
3. 一般 IDE 都会针对 `.eslintrc.js` 、`.prettierrc.js` 等这种特殊的配置文件设置特殊的文件样式，在目录中非常醒目。

```js
module.exports = {
    plugins: ['@babel/plugin-transform-arrow-functions'],
    presets: [
        [
            '@babel/preset-env',
            {
                targets: {
                    chrome: '52' // 目标浏览器
                }
            }
        ]
    ]
};
```

`.babelrc.js` 配置文件是针对文件夹的，即该配置文件所在的文件夹包括子文件夹都会应用此配置文件的设置，而且下层配置文件会覆盖上层配置文件，通过此种方式可以给不同的目录设置不同的规则。

而 `babel.config.js` 虽然写法和 `.babelrc.js` 一样，但前者是针对整个项目，一个项目只有一个放在项目根目录。如果两种类型的配置文件都存在，后者会覆盖前者的配置。

## Plugins 和 Presets

有了配置文件，接下来就是要通过配置文件告诉 babel 编译哪些内容，然后还要引入对应的编译插件 `Plugins`，比如上面示例中转换箭头函数的插件 `@babel/plugin-transform-arrow-functions`。

但是有个问题，我们总不能一个个的引入这些插件来对应转化我们用到的每个新特性，于是有了一个东西叫做预设 `Presets`。

预设其实就是一个预先设定的插件列表，使用一个预设就是将这个预设规定的全部插件安装并使用。比如预设 `@babel/preset-es2015` 可以转换 es2015 的特性（for-of、class、模板字符串等），预设 `@babel/preset-es2017` 可以转换 es2017 的特性（async/await 等）。

这时又有一个问题，随着 js 语法的更新，这些 preset 会越来越多，我们还是要一个个的引入这些预设。于是 babel 推出了 `preset-env` 预设，这是一个官方推荐使用的智能预设，只要安装这一个 preset，就会根据你设置的目标浏览器，自动将代码中的新特性转换成目标浏览器支持的代码。

## @babel/plugin-transform-runtime 和 @babel/runtime

在用 babel 编译时，有些功能需要一些工具函数来辅助实现，比如 class 的编译。

```js
class People {}

// babel编译后
'use strict';

function _classCallCheck(instance, Constructor) {
    if (!(instance instanceof Constructor)) {
        throw new TypeError('Cannot call a class as a function');
    }
}

var People = function People() {
    _classCallCheck(this, People);
};
```

编译后的代码中，_classCallCheck 就是一个辅助功能实现的工具函数。如果多个文件中都用到了class，每一个文件编译后都生成一个工具函数，最后就会产生大量重复代码，平白增加文件体积。

`@babel/plugin-transform-runtime` 这个插件就是为了解决这个问题，它会将这些工具函数转换成引入的形式。

安装设置完成之后再编译结果如下：

```js
"use strict";

var _interopRequireDefault = require("@babel/runtime/helpers/interopRequireDefault");

var _classCallCheck = _interopRequireDefault(require("@babel/runtime/helpers/classCallCheck"));

var People = function People() {
  (0, _classCallCheck["default"])(this, People);
};

```

_classCallCheck 这个工具函数已经变成从 `@babel/runtime` 这个包中引入，不会再产生单独的工具函数代码。所以还要安装 `@babel/runtime` 这个依赖包，在项目打包的时候才不会报错。

```shell
npm i @babel/plugin-transform-runtime -D
npm i @babel/runtime
```

```js
module.exports = {
    presets: ['@babel/preset-env'],
    plugins: ['@babel/plugin-transform-runtime']
};
```

注意: `@babel/runtime` 并不是开发依赖，而是项目生产依赖。编译时使用了 `@babel/plugin-transform-runtime`，项目就要依赖于 `@babel/runtime`，所以这两个东西是一起使用的。


## @babel/polyfill

babel 可以转化一些新的语法特性，但是对于新的内置函数（Promise、Set、Map）、静态方法（Array.from、Object.assign）、实例方法（Array.prototype.includes）这些就需要 `@babel/polyfill` 来解决，`@babel/polyfill` 会完整模拟一个 ES2015+ 的环境。

```js
module.exports = {
    presets: [
        [
            '@babel/preset-env',
            {
                useBuiltIns: 'usage', // usage: 按需引入; entry: 入口引入（整体引入）; false: 不引入 polyfill
                corejs: 2  // 2: corejs@2; 3: corejs@3
            }
        ]
    ]
};
```

corejs 是一个给低版本的浏览器提供接口的库，也是 polyfill 功能实现的核心，此处指定的是引入 corejs 的版本，所以还需要安装指定版本的 corejs 库也作为生产依赖。

```shell
npm i @babel/polyfill core-js@2
```