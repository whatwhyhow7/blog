---
layout: post
title: JavaScript 实现上下键选择推荐词
tags: [Front-End]
---

**摘要：**这里说的推荐词，其实是指当用户点击一个文本框时，从文本框中自动弹出的一个下拉列表，一般为用户的搜索历史记录或者服务器根据用户搜索文本匹配的推荐列表，用英文的 suggestion 形容还算到位。最近实现了一个可以用方向键上下选择 suggestion 的需求。功能实现起来并不复杂，记录一下以备未来之需。
{:.message}

```js
$(document).on('keydown', function (event) {
    let self = this;
    if (event.keyCode === 38 || event.keyCode === 40 || event.keyCode === 13) {
        event.preventDefault();
        let $cur = self.$tmpl.find('li.hover'); // 鼠标 hover 上的当前一条
        if (event.keyCode === 13) {
            // 回车键
            $cur.length ? $cur.trigger('click') : self.destroy(); // 如果有被鼠标选中的当前一条，则点击；否则移除 suggestion 的 dom
        } else {
            let $li = self.$tmpl.find('li'),
                num = $li.length, // suggestion 总条目
                $target; // 目标条目

            if (event.keyCode === 38) {
                // 方向键上
                $cur = $cur.length ? $cur : $li.eq(0); // 如果没有任何一条被鼠标选中，默认为第一条
                $target = $cur.prev().length ? $cur.prev() : $li.eq(num - 1); // 目标条目为当前条目的上一条，如果没有上一条，则目标条目为最后一条
            } else if (event.keyCode === 40) {
                // 方向键下
                $cur = $cur.length ? $cur : $li.eq(num - 1); // 如果没有任何一条被鼠标选中，默认为最后一条
                $target = $cur.next().length ? $cur.next() : $li.eq(0); // 目标条目为当前条目的下一条，如果没有下一条，则目标条目为第一条
            }
            $cur.removeClass('hover');
            $target.addClass('hover');
        }
    }
});
```