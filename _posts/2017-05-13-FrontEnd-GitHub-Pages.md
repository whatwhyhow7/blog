---
layout: post
title: 搭建自己的 GitHub Pages
tags: [Front-End]
---

**摘要：**以前不觉得，入坑前端后，觉得是时候弄一个自己的个人网站了，写写技术博客，上传些平时拍过的照片，再学别人弄个有逼格的在线简历啥的。搞前端还是得弄弄面子工程。花了几天时间，终于分别弄好了[个人主页](https://whatwhyhow7.github.io/)、[技术博客](https://whatwhyhow7.github.io/blog/)、[摄影作品](https://whatwhyhow7.github.io/photo/) 和 [在线简历](https://whatwhyhow7.github.io/resume/) 这4个小站。乘着刚刚搭建完成，总结一下使用 GitHub Pages 搭建个人网站的主要步骤。
{:.message}

其实搭建个人网站的方法有很多，比如可以使用早前的 [WordPress](https://wordpress.org/) 和现在比较流行的的 [Wix](https://www.wix.com/) 等等。但是我选择了 [GitHub Pages](https://pages.github.com/)，因为更新网站内容非常方便，而且每次更新都可以和 GitHub 的 contributions 关联起来。当你在 GitHub 上没有其他可以保证你经常有 push 操作的项目时，只要你坚持更新 GitHub Pages，那么依然可以让你的 GitHub contributions 保持常绿状态。这么一举两得的事情为什么不用呢。

## 第一步：新建项目仓库

这个第一步的前提是你已经有了一个 GitHub 账号。如果没有，还是先注册一个。然后在 GitHub 上新建一个项目仓库，命名为 `username.github.io`，这里的 username 必须是 GitHub 账号的用户名。比如我的 GitHub 账号用户名是 whatwhyhow7，所以我新建的项目名应该为 whatwhyhow7.github.io。填好项目名称后，其他保持默认点击确认按钮后一个项目就新建完成了。

## 第二步：选择主题并安装

进入刚刚新建的项目设置页面，可以找到 `GitHub Pages` 设置项，这时的显示应该为

![GitHub Pages Settings](/blog/assets/img/docs/GitHub-Pages/01.png)