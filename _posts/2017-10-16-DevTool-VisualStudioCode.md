---
layout: post
title: Visual Studio Code 使用手册
tags: [Dev-Tool]
---

**摘要：**我是一个半路出家的前端工程师，受最早写 Java 都用 Eclipse 的影响，后来不管是改用 IntelliJ IDEA 写 Java，还是用 Webstorm 做 IDE 或用 Sublime 做编辑器，都会把快捷键设置成 Eclipse Keymap，因为实在是记不住那么多新的快捷键。所以其实很早前就被安利过 VS Code，但还是因为上述的原因一直懒得尝试。最近由于工作内容的调整（开始接手搜狗翻译前端项目），而且既然已经决定转型做一名前端工程师了，有理由告诉自己是时候忘掉 Eclipse 了。那么就改头换面入坑 VS Code 吧。
{:.message}

## 快捷键

记录一些 Windows 下常用的快捷键，Mac 下的快捷键等有钱且需要买的时候再更新吧……

### 项目文件相关

| 类型 | 快捷键 |
| ---: | :--- |
| 打开命令面板 | `F1` |
| 文件重命名 | `F2` |
| 打开一个新窗口（查看多个项目） | `Ctrl + Shift + N` |
| 新建一个文件 | `Ctrl + N` |
| 跳转到文件 | `Ctrl + P` |
| 打开或关闭终端面板 | `Ctrl + J` |
| 分隔当前窗口或终端 | `Ctrl + \` |
| 在已打开文件之间切换 | `Ctrl + Tab`，`Alt + Left`，`Alt + Right` |

### 编辑修改相关

| 类型 | 快捷键 |
| ---: | :--- |
| 选中整行 | `Ctrl + I` |
| 将选择添加到下一个查找匹配项 | `Ctrl + D` |
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

## 全局设置

全局设置以及部分插件需要在用户设置右侧的 json 配置文件中进行配置。鼠标焦点不在编辑区域时使用 `Ctrl + ,` 可打开用户设置。

例如，VS Code 内置集成了可以执行指令的终端，可以使用以下配置将默认的 `shell` 设置成 `Git Bash`（如果本地有安装的话）：

```js
"terminal.integrated.cursorBlinking": true,
"terminal.integrated.cursorStyle": "line",
"terminal.integrated.shell.windows": "C:\\Program Files\\Git\\bin\\bash.exe"
```

另外，在‪ `C:\Program Files\Git\etc\bash.bashrc` 中可以添加 `alias ll="ls -la"` 让 `Git Bash` 支持 `ll` 命令。


## 安装插件

* Bracket Pair Colorizer：对括号对进行着色。

* Code Runner：轻松测试很多语言的代码片段而不用搭建响应的运行环境。

* One Dark Pro：个人比较喜欢的一个主题插件，配色很舒服。

* Output Colorizer: 给控制台输出着色。

* Path Intellisense：智能提示路径和目录层级，墙裂推荐。

* Project Manager：快速切换常用的项目，再也不用一个项目一个项目的打开文件夹了。

* XML Tools：格式化 xml 文件。

* vscode-icons 或 Material Icon Theme：文件图标主题。

## 参考资料

* [官方下载地址](https://code.visualstudio.com/)

* [非官方中文手册](https://jeasonstudio.gitbooks.io/vscode-cn-doc/content/)