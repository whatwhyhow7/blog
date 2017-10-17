---
layout: post
title: Visual Studio Code
tags: [Dev-Tool]
---

**摘要**: 我是一个半路出家的前端工程师，受最早写 Java 都用 Eclipse 的影响，后来不管是改用 IntelliJ IDEA 写 Java，还是用 Webstorm 做 IDE 和用 Sublime 做编辑器，都会把快捷键设置成 Eclipse Keymap，因为实在是记不住新的快捷键。其实很早前就被安利过 VS Code，但还是因为上述的原因一直懒得尝试。最近由于工作内容的调整（开始接手搜狗翻译前端项目），便心血来潮想改头换面，于是就入坑 VS Code 了。
{:.message}

## 快捷键

### 项目文件相关

| 类型 | 快捷键 |
| ---: | :--- |
| 打开命令面板 | `F1` |
| 文件重命名 | `F2` |
| 打开一个新窗口（查看多个项目） | `Ctrl + Shift + N` |
| 新建文件 | `Ctrl + N` |
| 跳转到文件 | `Ctrl + P` |
| 在已打开文件之间切换 | `Ctrl + Tab`，`Alt + Left`，`Alt + Right` |
| 在当前窗口中切出一个新的编辑器（最多3个）| `Ctrl + \`，也可以按住 Ctrl 并点击文件名 |

### 编辑修改相关

| 类型 | 快捷键 |
| ---: | :--- |
| 选中整行 | `Ctrl + I` |
| 点击一行进行整行剪切、复制、粘贴 | `Ctrl + X`、`Ctrl + C`、`Ctrl + V` |
| 代码格式化 | `Shift + Alt + F` |
| 上下移动一行 | `Alt + Up`、`Alt + Down` |
| 向上向下复制一行 | `Shift + Alt + Up`、`Shift + Alt + Down` |
| 移动到文件开头或结尾 | `Ctrl + Home`、`Ctrl + End` |
| 光标连续选择多处 | `Alt + Click 光标` 或者 `Ctrl + Alt + Down`、`Ctrl + Alt + Up` |
| 同时选中所有匹配的选区 | `Ctrl + Shift + L` |
| 回退上一个光标操作处 | `Ctrl + U` |
| 跳转到变量定义处 | `F12` |
| 列出变量所有的引用 | `Shift + F12` |
| 跳转到符号或段落 | `Ctrl + shift + O` |
| 跳转到行 | `Ctrl + G` |

## 插件

全局设置以及部分插件需要在用户设置右侧的 json 文档中进行配置。使用 `Ctrl + ,` 打开用户设置。

* Project Manager：快速切换常用的项目，再也不用一个项目一个项目的打开文件夹了。

* HTML Snippets：可以在不输入 < 的情况下提示。

* ESLint：检测 js 必备。

* Bracket Pair Colorizer：对括号对进行着色。

* Output Colorizer: 输出着色。

* vscode-icons 或 Material Icon Theme：文件图标主题。

* beautify：格式化html，css，js。

* background：可以把编辑器背景设置成喜欢的图片。参考配置如下：
```js
"background.useDefault" : false,
"background.style" : {
    "opacity" : 0.06
  },
"background.customImages" : [
    "file:///C:/Users/whatwhyhow7/Desktop/Wallpaper/geek.jpg"
]
```

## 参考资料

* [官方下载地址](https://code.visualstudio.com/)

* [非官方中文手册](https://jeasonstudio.gitbooks.io/vscode-cn-doc/content/)