---
layout: post
title: Babel 使用指南
tags: [Dev-Tool]
---

**摘要：**关于 Babel，一直使用得是稀里糊涂。安装了一堆包，说实话都有哪些作用和区别其实是说不上来的。再过几天就要离职了，暂时工作上没有其他事情，决定一探 Babel 的究竟。
{:.message}

## 怎么读

babel 是一个 Javascript 编译器，通俗点说，其作用就是将 Javascript 最新的语法和特性进行转换，以保证依然能够在不支持新语法和新特性的浏览器上正常运行。

所以，babel 实质上是一个工具。那么这个工具应该怎么读呢？这里明确一下，正确的发音应该是：[ˈbeɪbl]，类似于“掰啵”。

## 安装

`@babel/core` 为 babel 的基础包，是必须引入的。

现在的前端项目应该都用 `webpack` 了，所以可以一起安装 `babel-loader`，并且在 `webpack` 中进行配置:

```shell
npm i -D @babel/core babel-loader 
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
    presets: ['@babel/preset-env']
};
```

`.babelrc.js` 配置文件是针对文件夹的，即该配置文件所在的文件夹包括子文件夹都会应用此配置文件的设置，而且下层配置文件会覆盖上层配置文件，通过此种方式可以给不同的目录设置不同的规则。

而 `babel.config.js` 虽然写法和 `.babelrc.js` 一样，但前者是针对整个项目，一个项目只有一个放在项目根目录。如果两种类型的配置文件都存在，后者会覆盖前者的配置。

## Plugins 和 Presets

有了配置文件，接下来就是要通过配置文件告诉 babel 编译哪些内容，然后还要引入对应的编译插件（Plugins），比如转换箭头函数的插件。