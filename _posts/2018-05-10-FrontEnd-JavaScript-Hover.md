---
layout: post
title: JavaScript 实现 hover 下拉菜单
tags: [Front-End]
---

**摘要：**hover 一个按钮或者链接后弹出一个下拉菜单是网页中常见的功能布局。看似简单，但其中的难点在于在下拉菜单中 hover 也能保证功能的正常。这里的实现主要是依靠定时器分别判断了下拉菜单入口和下拉菜单的 `mouseenter` 和 `mouseleave` 状态。
{:.message}

```js
const Hover = {

    hover ($showBtn, $showBox, options) {

        if (!$showBtn.length || !$showBox.length) {
            return;
        }

        let timer = null,
            top = parseInt($showBox.css('top'), 10),
            opt = options || {},
            activeClass = opt.activeClass || '';

        function leaveHandler (e) {
            timer = setTimeout(function () {
                $showBtn.removeClass(activeClass);
                $showBox.stop().animate({
                    'top': top,
                    'opacity': 0
                }, 200, function () {
                    $(this).hide();
                });
            }, 200);
        }

        function enterHandler () {
            clearTimeout(timer);
            $showBtn.addClass(activeClass);
            $showBox.show().stop().animate({
                'top': opt.top,
                'opacity': 1
            }, 300);
        }

        $showBox.on('mouseenter', enterHandler);
        $showBtn.on('mouseleave', leaveHandler);

        $showBtn.on('mouseenter', enterHandler);
        $showBox.on('mouseleave', leaveHandler);
    }
};

module.exports = Hover;

```

使用时调用：

```js
Hover.hover($showBtn, $showBox, {
    'activeClass': 'btn-active',
    'top': 30
});
```