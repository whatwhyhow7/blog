---
layout: post
title: 移动端下载或唤起本地 APP
tags: [Front-End]
---

**摘要：**移动端常见的 APP 推广就是在页面底端固定一个浮层。如果手机上已经装了该 APP，点击后则唤起该 APP；如果没有安装，点击后会跳转到 APP 的下载地址。但是由于不管 iOS 还是 Android，浏览器都不可能预知本地是否安装了某个 APP 的，所以类似功能实现的原理大致都是使用 APP 内设置的 URL scheme。一个 APP 可以设置一个或多个打开自己的 URL scheme。比如，微信就注册自己能被 `weixin://` 打开。URL scheme 的载体可以使用一个不可见的 `iframe`，并通过两个定时器分别引导未安装 APP 的手机跳转到下载页面，或已安装 APP 的手机打开 APP 后回退依然可保持未跳转下载页面的状态。
{:.message}

```js
function openOrDownload () {
    var iframe = document.createElement('iframe'),
        delay = 1500,
        timer01,
        timer02;

    iframe.style.display = 'none';
    iframe.src = 'schemeURL';
    document.body.appendChild(iframe);

    timer01 = setTimeout(function () {
        document.body.removeChild(iframe);
        timer01 && clearTimeout(timer01);
        location.href = 'downloadURL';
    }, delay);

    timer02 = setTimeout(function () {
        timer02 && clearTimeout(timer02);
        location.reload();
    }, delay + 100);
}
```