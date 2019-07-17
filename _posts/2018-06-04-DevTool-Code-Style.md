---
layout: post
title: ESLint + Prettier + husky + lint-staged
tags: [Dev-Tool]
---

**摘要：**关于以上几个名词，在接触过几个前端工程之后应该都不会太陌生。这里不展开详细描述各个工具的作用是什么了，仅仅把相关基本的配置记录一下，方便拿来直接使用。
{:.message}

## 安装

```shell
npm i eslint -D
npm i eslint-config-prettier -D
npm i eslint-config-standard -D
npm i eslint-friendly-formatter -D
npm i eslint-loader -D
npm i eslint-plugin-html -D
npm i eslint-plugin-import -D
npm i eslint-plugin-node -D
npm i eslint-plugin-prettier -D
npm i eslint-plugin-promise -D
npm i eslint-plugin-standard -D
npm i husky -D
npm i lint-staged -D
npm i prettier -D
```

## 配置

以下的每一个配置都是最基础的配置，应该可以适用于小型项目，如果需要扩展，请查阅各个工具包的官方文档。

#### package.json

`husky` 负责各种 `git hook`。这里主要用到 `pre-commit` 这个 `hook`，在执行 `commit` 之前，运行 `lint-staged`。

`lint-staged` 用于对 `git` 暂存区中的文件执行代码检测。

`**/*.js`，表示在 `git add` 操作之前，对暂存区中所有 `.js` 文件依次执行 `prettier --write` 和 `eslint --fix` 操作。

`**/*.{html,json,md}`，表示在 `git add` 操作之前，对暂存区中所有 `.html`, `.json`, `.md` 文件依次执行 `prettier --write` 操作。这里没有 `eslint --fix` 操作是因为 `eslint` 只对 `.js` 文件和 `.html` 文件中的 `<script></script>` 部分有效。

在 `package.json` 中添加如下的配置，并根据需要酌情调整：

```js
"husky": {
    "hooks": {
        "pre-commit": "lint-staged"
    }
},
"lint-staged": {
    "**/*.js": [
        "prettier --write",
        "eslint --fix",
        "git add"
    ],
    "**/*.{html,json,md}": [
        "prettier --write",
        "git add"
    ]
},
```

#### .eslintrc.js

在项目根目录下添加 `.eslintrc.js` 文件，并根据需要酌情调整：

```js
module.exports = {
    env: {
        browser: true,
        es6: true,
        node: true
    },
    parserOptions: {
        ecmaVersion: 2018,
        sourceType: 'module'
    },
    extends: [
        'standard', 
        'plugin:prettier/recommended'
    ],
    plugins: [
        'html', 
        'prettier'
    ],
    rules: {
        'prettier/prettier': 'error',
        'no-use-before-define': 'error'
    }
};
```

#### .prettierrc.js

在项目根目录下添加 `.prettierrc.js` 文件，并根据需要酌情调整：

```js
module.exports = {
    printWidth: 150, // 一行的字符数，如果超过会进行换行
    tabWidth: 4, // 一个 tab 代表几个空格数
    singleQuote: true // 字符串是否使用单引号
};
```

#### webpack.config.base.js

这里主要是通过使用 `eslint-loader` 可以对正在编辑的 `.js` 文件和 `.html` 文件中的 `<script></script>` 部分实时检测。

将以下配置添加在 `webpack` 配置 `rules` 的部分，并根据需要酌情调整：

```js
{
    test: /\.(js|html)$/,
    loader: 'eslint-loader',
    enforce: 'pre',
    include: [path.resolve(__dirname, 'src/pages/component')],
    options: {
        formatter: require('eslint-friendly-formatter'), // 友好的提示方式
        fix: true // 有问题直接修复
    }
}
```