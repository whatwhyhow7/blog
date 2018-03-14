---
layout: post
title: 搭建自己的 GitHub Pages
tags: [Front-End]
---

**摘要：**以前不觉得，入坑前端后，觉得是时候弄一个自己的个人网站了，写写技术总结，上传些平时拍过的照片，再学别人弄个有逼格的在线简历啥的。搞前端还是得弄弄面子工程。花了几天时间，终于分别弄好了[个人主页](https://whatwhyhow7.github.io/)、[技术博客](https://whatwhyhow7.github.io/blog/)、[摄影作品](https://whatwhyhow7.github.io/photo/) 和 [在线简历](https://whatwhyhow7.github.io/resume/) 这4个小站。乘着刚刚搭建完成，总结一下使用 GitHub Pages 搭建个人网站的主要步骤。
{:.message}

其实搭建个人网站的方法有很多，比如可以使用早前的 [WordPress](https://wordpress.org/) 和现在比较流行的的 [Wix](https://www.wix.com/) 等等。但是我选择了 [GitHub Pages](https://pages.github.com/)，因为更新网站内容非常方便，而且每次更新都可以和 GitHub 的 contributions 关联起来。当你在 GitHub 上没有其他可以保证你需要提交更新的项目时，只要你坚持更新 GitHub Pages，那么依然可以让你的 GitHub contributions 保持常绿状态。这么一举两得的事情为什么不用呢。

## 第一步：新建项目仓库

这个第一步的前提是你已经有了一个 GitHub 账号。如果没有，还是先注册一个。然后在 GitHub 上新建一个项目仓库，命名为 `username.github.io`，这里的 username 必须是 GitHub 账号的用户名。比如我的 GitHub 账号用户名是 whatwhyhow7，所以我新建的项目名应该为 whatwhyhow7.github.io。填好项目名称后，其他保持默认然后点击确认按钮后一个项目就新建完成了。

## 第二步：选择主题并发布

进入刚刚新建项目的 `Settings` 页面，找到 `GitHub Pages` 设置项，这时的显示应该为：

![GitHub Pages Settings](/blog/assets/img/docs/GitHub-Pages/01.png)

点击 `Choose a theme` 后选择一个主题，等几分钟后，在浏览器中输入 `https://whatwhyhow7.github.io/`，我们会发现以刚刚新建项目名称为地址的一个网页就在浏览器中渲染出来了，而且样式就是我们刚刚选择主题的样式。到这一步，我们的主页已经完成一半了。

## 第三步：自定义主题

尽管 GitHub Pages 为我们提供了几个还算不错的默认页面主题，但是对于做前端开发的同学肯定不会满意。这时我们就会考虑能不能在默认的主题基础上修改或者套用其他更好看的页面主题呢？答案自然是肯定的。

其实，GitHub Pages 官方使用的是一个叫做 [Jekyll](https://jekyllrb.com/) 的模板转换工具进行页面渲染的。我们只要将一个 GitHub 项目设置成为一个 `Jekyll` 工程项目，就可以将这个项目中的 `markdown` 文件和 `html` 模板转化为静态网页。

那么想使用自定义的主题，除了自己按照 `Jekyll` 规定的项目结构和配置进行编写以外，其实网上有很多别人写好的 [Jekyll模板](http://jekyllthemes.org/)，我们只需要选择其中自己喜欢的模板主题，`clone` 下来后将其对应的文件放入我们的项目目录中，推送到远程后更换别人的主题就算完成了。下图就是一个大致的 `Jekyll` 项目目录结构：

![Jekyll 项目结构](/blog/assets/img/docs/GitHub-Pages/02.png)

关于如何将 GitHub 中的项目保存在本地并将本地项目和 GitHub 远程仓库建立关联的设置可以参见 [Git 使用手册](/blog/2016/04/06/DevTool-Git/) 中 **必要设置** 一节中的说明。

## 第四步：Jekyll 项目本地编译

至此，网站的主题套用就算完成了。但是别人的主题终归只是一个模板，其中还有很多细节需要我们耐心地修改更换为我们自己的内容。对于文案调整这类比较小的修改，我们只需要在本地修改后推送到远程就可以生效了。但是对于样式和交互调整这种比较大的修改，需要反复调试才能完成。如果只是靠反复推送到远程查看效果就太影响效率了。所以我们需要在本地搭建可以运行 `Jekyll` 项目的环境，这样我们在本地就可以看到修改的效果，等到本地修改完成后再推送到远程实现网站的更新。



