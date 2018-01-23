---
layout: post
title: JavaScript 实现弹框拖拽
tags: [Front-End]
---

**摘要：**最近碰上一个在 JavaScript 中实现可拖拽弹框的需求。搜索了一下相关资料后，发现张鑫旭大神写过一个 [demo](http://www.zhangxinxu.com/wordpress/2010/03/javascript实现最简单的拖拽效果)。大神就是大神，写的非常好，浏览器兼容性处理的也非常好。但是 demo 中的实现可以把弹框拖出屏幕，这样的体验可能会被用户吐槽，也非常容易被老板当成 bug 报出来。阅读了一下对应的源码，添加了屏幕边界的判断并整理为一个模块，贴在这里以后需要时可以直接拿来使用。
{:.message}

```js
const Drag = {

    params: {
        left: 0,
        top: 0,
        currentX: 0,
        currentY: 0,
        flag: false
    },

    getCss (o, key) {
        return o.currentStyle ? o.currentStyle[key] : document.defaultView.getComputedStyle(o, false)[key];
    },

    drag (bar, target, callback) {

        var self = this;
        if (self.getCss(target, "left") !== "auto") {
            self.params.left = self.getCss(target, "left");
        }
        if (self.getCss(target, "top") !== "auto") {
            self.params.top = self.getCss(target, "top");
        }

        bar.onmousedown = function (event) {
            self.params.flag = true;
            if (!event) {
                event = window.event;
                bar.onselectstart = function () {
                    return false;
                }
            }
            var e = event;
            self.params.currentX = e.clientX;
            self.params.currentY = e.clientY;
        };

        document.onmouseup = function () {
            self.params.flag = false;
            if (self.getCss(target, "left") !== "auto") {
                self.params.left = self.getCss(target, "left");
            }
            if (self.getCss(target, "top") !== "auto") {
                self.params.top = self.getCss(target, "top");
            }
        };

        document.onmousemove = function (event) {
            var e = event || window.event;
            if (self.params.flag) {
                var nowX = e.clientX, nowY = e.clientY;
                var disX = nowX - self.params.currentX, disY = nowY - self.params.currentY;
                var left = parseInt(self.params.left) + disX;
                var right = document.body.clientWidth - target.offsetWidth;
                var top = parseInt(self.params.top) + disY;
                var bottom = document.body.clientHeight - target.offsetHeight;
                var scrollTop = document.documentElement.scrollTop || document.body.scrollTop;
                var scrollLeft = document.documentElement.scrollLeft || document.body.scrollLeft;

                if (top <= scrollTop) {
                    target.style.top = scrollTop + "px";
                } else if (top >= (scrollTop + bottom)) {
                    target.style.top = scrollTop + bottom + "px";
                } else {
                    target.style.top = top + "px";
                }

                if (left <= scrollLeft) {
                    target.style.left = scrollLeft + "px";
                } else if (left >= (scrollLeft + right)) {
                    target.style.left = scrollLeft + right + "px";
                } else {
                    target.style.left = left + "px";
                }

                if (event.preventDefault) {
                    event.preventDefault();
                }
                return false;
            }
        }
    }
}

module.exports = Drag;
```


