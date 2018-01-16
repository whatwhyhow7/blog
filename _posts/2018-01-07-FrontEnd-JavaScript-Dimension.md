---
layout: post
title: JavaScript 中各种尺寸的计算方式
tags: [Front-End]
---

**摘要：**前端的日常开发中，总是免不了碰到一些常见的需求，比如：在屏幕正中弹出一个浮层、滚动到屏幕底部时加载更多数据等等。这些需求的实现有一个共性，那就是需要计算屏幕宽高以及屏幕的滚动距离。这里就针对 JavaScript 中各种尺寸的计算方式进行一个简单的整理，希望能记住并熟练应用。
{:.message}

## 屏幕宽高

```js
$(window).width()
$(window).height()
$(window).scrollTop()
$(window).scrollLeft()
```

