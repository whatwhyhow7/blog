---
layout: post
title: Mac 开发环境搭建
tags: [Dev-Tool]
---

**摘要：**之前都是在 Windows 环境开发，自己其实是一直想买一台 Mac 的，但又觉得用处也不是很大，在准备买房的档口还是节省点开支吧。无奈公司给配了一台 Mac，还真的是很无奈呢‍🤷。 ‍捣鼓了两天终于是把日常开发用的环境搭了起来，遂赶紧记录一下 Mac 开发环境的搭建和设置。
{:.message}

## npm 全局安装设置

在 Mac 下全局安装 npm 包时，会因为默认安装目录 `/usr/local/lib/node_modules` 的权限问题报 `npm ERR! code: 'EACCES'` 的错误。

虽然可以通过使用 sudo 或者改变目录权限等常规命令解决，但是不是最好的方式。

我们可以通过修改 npm 全局安装包的目录解决这个问题：

1. 创建全局目录：`mkdir ~/.npm-global`

2. 配置 npm 使用新路径：`npm config set prefix '~/.npm-global'`

3. 在 `~/.bash_profile` 文件中添加环境变量：`export PATH=~/.npm-global/bin:$PATH` 

4. 更新系统变量：`source ~/.bash_profile`

## Java 环境变量

在 `~/.bash_profile` 文件中添加（将以下的 `jdk-11.0.3.jdk` 改为你安装的 JDK 版本）：

```shell
export JAVA_HOME=/Library/Java/JavaVirtualMachines/jdk-11.0.3.jdk/Contents/Home
export PATH=$JAVA_HOME/bin:$PATH
export CLASSPATH=.:$JAVA_HOME/lib/dt.jar:$JAVA_HOME/lib/tools.jar
```

更新系统变量：`source ~/.bash_profile`

## 安装 iTerm2

官方地址：[https://www.iterm2.com/downloads.html](https://www.iterm2.com/downloads.html)

## 安装 Oh My ZSH!

* 通过 `cat /etc/shells` 命令可以查看当前系统可以使用哪些 shell

* 通过 `echo $SHELL` 命令可以查看当前正在使用的 shell

* 如果当前的 shell 不是 `zsh`，可以通过 `chsh -s /bin/zsh` 命令可以将 shell 切换为 `zsh`，重启终端之后即可生效。

* 将 shell 切换为 `zsh` 之后，我们就可以通过以下的命令安装 [Oh My ZSH!](https://ohmyz.sh/) 了：

```shell
sh -c "$(curl -fsSL https://raw.github.com/robbyrussell/oh-my-zsh/master/tools/install.sh)"
```

#### 设置环境变量

在 `~/.zshrc` 中添加 `source ~/.bash_profile`， 让 `zsh` 可以使用 `bash` 下的命令，并通过 `source ~/.zshrc` 使配置生效。

#### 配置 agnoster 主题

`Oh My ZSH!` 支持的主题有很多：[https://github.com/robbyrussell/oh-my-zsh/wiki/Themes](https://github.com/robbyrussell/oh-my-zsh/wiki/Themes)

我选择使用 `agnoster` 主题：通过 `vim ~/.zshrc`，设置 `ZSH_THEME="agnoster"` 对主题进行修改，并通过 `source ~/.zshrc` 使配置生效。

#### 安装配色方案和字体

对于一般的主题，通过以上的操作就可以设置成功了。但是 `agnoster` 主题还需要安装对应的配色方案和字体。

* Solarized Dark 配色方案

下载地址：[https://ethanschoonover.com/solarized/#features](https://ethanschoonover.com/solarized/#features)

下载完成之后解压，在 `iTerm2` 的 `Preferences - Profiles - Colors - Color Presets - Solarized Dark` 即可设置终端配色。

* 特殊字体

下载地址：[https://github.com/powerline/fonts](https://github.com/powerline/fonts)

下载完成之后解压，执行其中的 `install.sh` 文件，在 `iTerm2` 的 `Preferences - Profiles - Text` 中同时将 `Font` 和 `Non—ASCII Font` 设置为 `Meslo LG M DZ Regular for Powerline` 即可。

* VS Code 终端设置

执行以上步骤后，`iTerm2` 中的主题就设置完成了。但是如果在 VS Code 的终端中也想应用 `zsh` 的话，会发现有类似乱码的情况出现。这时还得进行以下设置：

下载另一个 [字体文件](https://github.com/abertsch/Menlo-for-Powerline/blob/master/Menlo%20for%20Powerline.ttf) 并安装。

在 VS Code 中的 `settings.json` 中添加以下配置：

```js
"terminal.external.osxExec": "iTerm.app",
"terminal.integrated.shell.osx": "/bin/zsh",
"terminal.integrated.fontFamily": "Menlo for Powerline"
```

## 常用快捷键

| 类型 | 快捷键 |
| ---: | :--- |
| 最大化、最小化窗口 | `control + command + f` |
| 截图 | `shift + command + 5` |






