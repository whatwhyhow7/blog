---
layout: post
title: JavaScript 获取页面内的选择文本
tags: [Front-End]
---

**摘要：**刚刚上线了 [搜狗翻译](https://fanyi.sogou.com/) PC 端的二期优化需求，终于有空写写自己的博客了。本次的二期需求中有一项是划词翻译，即用户使用鼠标在页面内选取一段文本，会在选取文本的右下方弹出一个小浮层，显示其选取文本的翻译内容。关于获得选取文本这里的技术点大体上是使用 [window.getSelection](https://developer.mozilla.org/zh-CN/docs/Web/API/Window/getSelection) 这个 API。但是在开发中发现不同浏览器对于这个 API 的兼容还是比较坑的。所以有必要在此做个记录。
{:.message}

## 获取页面内的选择文本

话不多少，直接上代码。注释里有针对不同浏览器的兼容说明，以后应该可以直接拿来使用：

```js
getSelection () {
    let selection = '';
    if (window.getSelection) {
        let activeElement = document.activeElement;
        if (activeElement && (activeElement.nodeName === "TEXTAREA" || activeElement.nodeName === "INPUT")) {
            selection = activeElement.value.substring(activeElement.selectionStart, activeElement.selectionEnd); // 针对 FireFox textarea input 的特殊处理
        } else {
            selection = window.getSelection(); // FireFox, Safari, Chrome
        }
    } else if (document.getSelection) {
        selection = document.getSelection(); // IE10
    } else if (document.selection) {
        selection = document.selection.createRange().text; // IE6+10-
    }
    return selection.toString().replace(/^\s+/g, "").replace(/\s+$/g, "");
}
```

## 移除页面内的选择“记忆”

一般情况下，用户多次选取页面文本，后一次都会覆盖前一次的选取，并不需要主动移除上次的选取“记忆”。比如说，当用户选取完一段文本后，此时选取“记忆”便更新为当前的选取。当用户点击空白区域后，理论上当前的选取“记忆”应该要被清空的，因为用户的意图是放弃选取了。但是在 Chrome 中发现，当用户点击空白区域后，当前的选取“记忆”还在，必须主动清除才可以。

```js
removeSelection () {
    if (window.getSelection) {
        window.getSelection().removeAllRanges();
    } else if (document.getSelection && document.getSelection.empty) {
        document.getSelection().empty();
    } else if (document.selection && document.selection.empty) {
        document.selection.empty();
    }
}
```

通常情况下，当我们在页面中选取一段文本后，所选取的文本区域会被蓝色的样式所包含。但是，上述这种主动清除会同时清除掉该蓝色样式，如果在选取后立马清除会给人一种压根没有选取到的错觉。所以这时就需要做一个如下的判断：

如果当前的选取文本和上次的选取文本一致，则主动清除；如不一致，则在鼠标位置弹出浮层。

## 划取功能的简单实现

综上所述，一个划取功能的简单实现就如以下代码所示，同时也附上获得鼠标位置的计算方法：

```js
let self = this;
$('body').on('click', function (e) {
    let selection = self.getSelection();
    if (selection) {
        if (selection !== self.selection) {
            e = e || window.event;
            let scrollX = document.documentElement.scrollLeft || document.body.scrollLeft, // 页面滚动宽度
                scrollY = document.documentElement.scrollTop || document.body.scrollTop, // 页面滚动高度
                x = e.pageX || e.clientX + scrollX, // 弹出浮层绝对定位的 left 值
                y = e.pageY || e.clientY + scrollY; // 弹出浮层绝对定位的 top 值
            self.create(selection, x, y);
            self.selection = selection;
        } else {
            delete self.selection;
            self.removeSelection();
        }
    }
});
```
