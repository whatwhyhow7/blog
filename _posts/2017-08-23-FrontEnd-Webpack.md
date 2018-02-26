---
layout: post
title: 初窥 Webpack
tags: [Front-End]
---

**摘要：**当下 webpack 真是有点火，要说没听过或者不懂 webpack 都不好意思跟人说是个搞前端的。这里我尝试写一个 webpack 的简易上手教程，证明一下我也算是个“前端配置工程师”了。
{:.message}

## What is webpack?

[webpack](https://webpack.js.org/) 不是一门语言，也不是什么框架，它其实就是一个工具：前端项目的模块打包工具。

它做的事情是：分析前端项目的结构，通过配置找到 js、css 等或需要预处理，或需要预编译，或需要嵌入到页面模板中的所有静态资源作为模块，再通过合适的 `loader` 将这些静态资源转换和打包成一个或多个静态文件，然后将其嵌入页面供浏览器使用，从而可以减少页面中资源的请求。

## Why webpack?

现在很多的网页其实都是功能丰富的应用，拥有着复杂的 JavaScript 代码和一大堆依赖的资源。为了简化开发的复杂度，前端社区涌现出了很多好的实践方法，比如说：

* 模块化：把复杂的业务逻辑和代码实现细化为小的文件。

* 扩展语言和预处理器：比如 TypeScript 这种在 JavaScript 基础上拓展的开发语言，能够实现目前版本的 JavaScript 不能直接使用的特性，之后还能转换为 JavaScript 文件使浏览器可以识别；还比如 Sass、Less 等 css 预处理器，可以像写程序一样写 css 文件。

这些改进大大提高了前端的开发效率，但是以上所有优化都需要挨个手动处理又是非常繁琐的。这就为 webpack 类工具的出现提供了需求。

## How to use webpack?

webpack 的工作方式是：把项目当做一个整体，通过一个给定的主文件（如 index.js），webpack 将从这个文件开始找到项目的所有依赖文件，使用 loaders 和 plugins 处理它们，最后打包成一个（或多个）浏览器可识别的 js 文件。

### 安装

webpack 可以使用 npm 安装，新建一个空的文件夹，在终端中转到该文件夹后执行以下命令就可以完成安装：

```shell
npm init
npm install --save-dev webpack
```

然后，在 `package.json` 中对 `scripts` 对象进行相关设置即可让 npm 引导 webpack 执行：

```js
"scripts": {
  "start": "webpack", // 对应的指令为 npm start
  "build": "webpack --display-error-details --progress --colors --watch" // 对应的指令为 npm run build
}
```

注：npm 的 `start` 命令是一个特殊的脚本名称，其特殊性表现在：在命令行中使用 `npm start` 就可以执行其对于的命令；而如果对应的脚本名称不是 `start`，则需要使用 `npm run {script name}` 才可以执行，比如 `npm run build`。

### 配置文件

新建一个名为 `webpack.config.js` 的文件，webpack 会自动引用这个文件中的配置选项。这个配置文件其实也是一个简单的 JavaScript 模块，可以把所有与打包相关的信息放在里面。

下面先贴一个比较常见的 webpack 配置感受一下，具体的设置后面再一一介绍：

```js
const webpack = require('webpack'); // webpack 自身模块
const ExtractTextPlugin = require('extract-text-webpack-plugin'); // 分离 css 和 js 的插件
const HtmlWebpackPlugin = require('html-webpack-plugin'); // html 模板插件

module.exports = {
  entry: __dirname + "/src/app.js", // webpack 大显身手的入口文件，其他文件资源都应与它建立关联，比如在入口文件中 require 其他相关文件
  output: {
    path: __dirname + "/dist", // 打包后文件的存放路径。__dirname 是 Node.js 中的一个全局变量，它指向当前执行脚本所在的目录
    filename: "[name]-[hash].js" // 打包后输出文件的命名格式
  },
  // webpack 本地服务器配置
  devServer: {
      contentBase: "./src", // 本地服务器所加载页面所在的目录
      port: 80, // 设置默认监听端口，如果省略，默认为 8080
      inline: true // 设置为 true，当源文件改变时会实时自动刷新页面
  },
  // loaders 配置
  module: {
    loaders: [
      { test: /\.css$/, loader: 'style-loader!css-loader' }, // 使用 ! 从右向左依次使用多个 loader
      { test: /\.less$/, loader: 'style-loader!css-loader!less-loader' }, 
      { test: /\.(png|jpg)$/, loader: 'url-loader?limit=8192'} // ? 相当于传递参数。小于 8k 的图片会被转为 base64 格式的内链 dataUrl, 其他的直接使用外链
    ]
  },
  // plugins 配置
  plugins: [
    new webpack.BannerPlugin('版权所有，翻版必究'), // 给打包后文件添加版权声明的插件
    new webpack.optimize.UglifyJsPlugin(), // 压缩打包文件的插件
    new ExtractTextPlugin('styles.css'), // 在 dist 文件夹中生成一个汇总的 styles.css 文件，需要将它作为一个单独的样式加入到 index.html 中
    new HtmlWebpackPlugin({
        template: __dirname + "/src/index.tmpl.html"
    })
  ],
  // 其他解决方案配置
  resolve: {
    root: __dirname, // 绝对路径，查找 module 的话从这里开始查找
    extensions: ['', '.js', '.json', '.scss'], // 自动扩展文件后缀名，意味着 require 模块可以省略不写后缀名
    // 模块别名定义，方便后续直接引用别名，无须多写长长的地址
    alias: {
      Utils : './src/js/common/utils.js' // 后续直接 require('Utils') 即可
    }
  }
};
```

#### [webpack 本地服务器 DevServer](https://webpack.js.org/configuration/dev-server/)

较为方便的开发场景就是让浏览器监听代码的修改，并自动刷新显示修改后的结果。webpack 提供了一个可选的本地开发服务器，这个服务器是基于 Node.js 构建的一个单独的组件，在 webpack 中进行配置之前需要单独安装它作为项目依赖：

```shell
npm install --save-dev webpack-dev-server
```

在 `package.json` 中的 `scripts` 对象中添加如下命令用以开启本地服务器，在终端中输入 `npm run server` 即可在本地的 80 端口查看结果：

```js
"scripts": {
  "server": "webpack-dev-server --open --progress --colors"
}
```

#### Loaders

[loaders](https://webpack.js.org/loaders/) 算是 webpack 提供的最激动人心的功能之一了。通过使用不同的 loader，webpack 就可以调用外部的脚本或工具来实现对不同格式的文件的处理。

比说，我们可以看到这样的情况：

```js
require('./a.css');
require('./b.less');

var img = document.createElement('img');
img.src = require('./img.png');
```

在一个 js 文件中, css 和 less 以及图片被直接引用了。实际上，最终 css 被转化为了 `<style>` 标签, 而图片可能被转化成 base64 格式的 dataUrl。这就得益于在 `webpack.config.js` 中配置好了对应的 `loader`。

loaders 需要单独安装并需要在 `webpack.config.js` 中的 `modules` 关键字下进行配置。一般来讲，loaders 的配置包括以下几方面：

* `test`：一个用以匹配 `loader` 所处理文件扩展名的正则表达式（必须）。

* `loader`：匹配到上面 `test` 文件的 `loader` 名（必须）。

* `include/exclude`：手动添加必须处理的文件（文件夹）或屏蔽不需要处理的文件（文件夹）（可选）。

* `query`：为 loaders 提供额外的设置选项（可选）。

常见的 `loader` 有：

##### [css-loader](https://webpack.js.org/loaders/css-loader/) 和 [style-loader](https://webpack.js.org/loaders/style-loader/)

这是两个处理 css 模块的 `loader`。`css-loader` 用来返回有 `@import` 和 `url()` 的 css 文件，`style-loader` 用来将 css 文件插入页面。二者组合在一起能够把 css 样式嵌入 webpack 打包后的 js 文件中。

```shell
npm install --save-dev css-loader style-loader
```

通常情况下，css 会和 js 打包到同一个文件中，并不会打包为一个单独的 css 文件，不过通过合适的配置 webpack 也可以把 css 打包为单独的文件。

#### Plugins

[plugins](https://webpack.js.org/plugins/) 就是 webpack 的插件了。插件是用来扩展 webpack 功能的，它们会在整个构建过程中生效并执行相关的任务。loaders 和 plugins 常常被弄混，但他们其实是完全不同的东西。

这么来说，loaders 是在打包构建过程中用来处理源文件的（js, css，scss...），一次处理一个；而插件并不直接操作单个文件，它直接对整个构建过程起作用。

webpack 有很多内置的插件，同时也有很多需要额外安装的第三方插件。想要使用一个插件，只需要 require() 它，然后在 `webpack.config.js` 中的 `plugins` 关键字部分添加该插件的一个实例。常用的插件有：

##### [HtmlWebpackPlugin](https://webpack.js.org/plugins/html-webpack-plugin/)

这个插件的作用是根据一个简单的 html 模板，生成一个自动引用打包后的 js 文件的新 html。

```shell
npm install --save-dev html-webpack-plugin
```

##### [ExtractTextWebpackPlugin](https://webpack.js.org/plugins/extract-text-webpack-plugin/)

这个插件的作用是分离打包后的 css 和 js，也就是不把 css 文件打包进最后生成的 js 文件当中。

```shell
npm install --save-dev extract-text-webpack-plugin
```