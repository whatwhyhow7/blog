---
layout: post
title: 搭建自己的 GitHub Pages
tags: [Dev-Tool]
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

其实，GitHub Pages 官方使用的是一个叫做 [jekyll](https://jekyllrb.com/) 的模板转换工具进行页面渲染的。我们只要将一个 GitHub 项目设置成为一个 `jekyll` 工程项目，就可以将这个项目中的 `markdown` 文件和 `html` 模板转化为静态网页。

那么想使用自定义的主题，除了自己按照 `jekyll` 规定的项目结构和配置进行编写以外，其实网上有很多别人写好的 [jekyll模板](http://jekyllthemes.org/)，我们只需要选择其中自己喜欢的模板主题，`clone` 下来后将其对应的文件放入我们的项目目录中，推送到远程后更换成别人的主题就算完成了。下图就是一个大致的 `jekyll` 项目目录结构：

![jekyll 项目结构](/blog/assets/img/docs/GitHub-Pages/02.png)

关于如何将 GitHub 中的项目保存在本地并将本地项目和 GitHub 远程仓库建立关联的设置可以参见 [Git 使用手册](/blog/2016/04/06/DevTool-Git/) 中 **必要设置** 一节中的说明。

## 第四步：Jekyll 项目本地编译

至此，网站的主题套用就算完成了。但是别人的主题终归只是一个模板，其中还有很多细节需要我们耐心地修改更换为我们自己的内容。对于文案调整这类比较小的修改，我们只需要在本地修改后推送到远程就可以生效了。但是对于样式和交互调整这种比较大的修改，需要反复调试才能完成。如果只是靠反复推送到远程查看效果就太影响效率了。所以我们需要在本地搭建可以运行 `jekyll` 项目的环境，这样我们在本地就可以看到修改的效果，等到本地修改完成后再推送到远程实现网站的更新。

那么就需要在本地安装 `jekyll` 环境了。以 `windows` 下的安装过程为例：

1. 安装 [Rails](http://railsinstaller.org/en)。`jekyll` 是使用 `ruby` 语言开发的，所以首先需要安装 `ruby` 环境。安装完成之后，在命令行中输入 `ruby -v` 来检查 `ruby` 是否已经正确安装。

2. 安装 [RubyGems](https://rubygems.org/pages/download)。`RubyGems` 是一个 `ruby` 的包管理器，用它可以很方便地在本地安装 `ruby` 应用。下载 zip 包并解压缩，找到 `setup.rb` 文件所在目录并执行 `ruby setup.rb`。安装完成之后，在命令行中输入 `gem -v` 来检查 `RubyGems` 是否已经正确安装。

3. 安装 `jekyll`。有了 `RubyGems` 之后安装 `jekyll` 就很容易了。在命令行中输入 `gem install jekyll`。安装完成之后，在命令行中输入 `jekyll -v` 来检查 `jekyll` 是否已经正确安装。

4. 以上都安装好之后，进入本地的 `jekyll` 项目，在命令行中输入 `jekyll serve -w` 便可实时地在本地 `http://localhost:4000/` 看到用模板搭建的网站了。如果遇到相关依赖报错的问题，可以参照上一步中安装 `jekyll` 的方法安装其他依赖。比如安装 `bundler` 依赖：`gem install bundler`，同样的，使用 `bundler -v` 来检查 `bundler` 是否已经正确安装。

## 最后

至此，我们的网站已经可以在本地进行预览了。后续我们要做的就是熟悉 `jekyll` 模板的结构，然后修改成为我们自己的网站，并且坚持写 `markdown` 以丰富网站内容。关于 `jekyll` 和 `markdown` 的相关语法直接去看对应的官方文档就好了，这里便不再赘述。