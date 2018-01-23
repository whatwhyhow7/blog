---
layout: post
title: JavaScript 中常见尺寸的计算方式
tags: [Front-End]
---

**摘要：**前端的日常开发中，总是免不了碰到一些常见的需求，比如：在屏幕正中弹出一个浮层、滚动到屏幕底部时加载更多数据等等。这些需求的实现有一个共性，那就是需要计算屏幕宽高以及屏幕的滚动距离。这里就针对 JavaScript 中各种尺寸的计算方式进行一个简单的整理，希望能记住并熟练应用。
{:.message}

```js
document.documentElement.clientWidth  || document.body.clientWidth  || $(window).width();  // 网页可见区域宽度
document.documentElement.clientHeight || document.body.clientHeight || $(window).height(); // 网页可见区域高度

document.documentElement.scrollLeft   || document.body.scrollLeft   || $(window).scrollLeft(); // 网页被卷去的宽度
document.documentElement.scrollTop    || document.body.scrollTop    || $(window).scrollTop();  // 网页被卷去的高度

document.documentElement.scrollWidth  || document.body.scrollWidth;  // 网页全文宽度（从最上滚到最下）
document.documentElement.scrollHeight || document.body.scrollHeight; // 网页全文高度（从最左滚到最右）

document.getElementById('obj').offsetWidth  || $('#obj').width();  // 某个元素的实际宽度
document.getElementById('obj').offsetHeight || $('#obj').height(); // 某个元素的实际高度

document.getElementById('obj').offsetLeft   || $('#obj').offset().left; // 某个元素的左边界到页面最左边的距离
document.getElementById('obj').offsetTop    || $('#obj').offset().top;  // 某个元素的上边界到页面最顶部的距离
```

