---
layout: post
title: Linux 中那些总是记不住的命令
tags: [Dev-Tool]
---

**摘要：**linux 中有些命令总是记不住。可能就是也不怎么常用，一年半载用一次的时候查一下然后解决了最后就撂过了，下次又得查一遍而且还总是觉得上次查的那个比较靠谱。所以还是最好把自身常用的一些命令先记下来，以后要是还记不住……那就来这里查……
{:.message}

### 磁盘满时，如何定位并删除相应文件

```shell
# 先看一下磁盘使用情况
df -h

# cd 进入那个比较满的目录，然后执行下面的命令，可以列出所有文件夹的大小
du -shx *

# 进入那个比较满的目录，找到那个占用空间较大的文件
ll -h

# 或是直接列出当前目录下占用空间最大的 10 的目录或文件，以 /search 目录为例：
du -a /search | sort -n -r | head -n 10

# 一般应该是某个日志文件，要是没啥用了清空或是删除即可
> filename

```

### 删除已输入的命令

在 linux 中有时候输入命令或字符时，比如说输入 Git 用户名，输错了一个字符，这时按 backspace 键并不能删除错误字符，反而会增加更多的错误字符。这时，下面的快捷键就大显身手了：

```shell
ctrl + w    删除光标之前的一个字符

ctrl + u    删除光标之前的所有字符
```

### 使用 [nvm](https://github.com/creationix/nvm) 安装 Node.js 和 Yarn

```shell
### 安装 nvm
curl -o- https://raw.githubusercontent.com/creationix/nvm/v0.33.8/install.sh | bash
source ~/.nvm/nvm.sh

### 安装 node.js
nvm install --lts

### 安装 yarn
sudo wget https://dl.yarnpkg.com/rpm/yarn.repo -O /etc/yum.repos.d/yarn.repo
curl --silent --location https://rpm.nodesource.com/setup_8.x | sudo bash -
yum install yarn
```