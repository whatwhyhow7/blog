---
layout: post
title: 关于函数节流和函数防抖
tags: [Front-End]
---

**摘要：**开发中有一些场景由于事件频繁被触发，因而频繁执行了 DOM 操作、资源加载等重行为，导致 UI 停顿甚至浏览器崩溃。比如处理 resize、scroll、mouseover 事件和文字输入、自动完成的 input、keyup 事件等等。像 scroll 事件，一次鼠标的滚动就会触发多次，而实际上在很短的时间内只需要处理一次回调，这就非常需要使用函数节流来避免处理多次回调逻辑。而像 window 的 resize 事件，实际需求大多为停止改变大小n毫秒后执行后续处理，这时又需要通过函数防抖来进行减少回调的优化。
{:.message}

所谓节流，就好比如果将水龙头拧紧直到水是以水滴的形式流出，这样每隔一段时间，就会有一滴水流出。也就是说预先设定一个执行周期，当调用动作的时刻大于等于执行周期才执行该动作，然后进入下一个新周期。

```js
function throttle(fn, delay = 300) {
    let canRun = true;
    return function() {
        if (!canRun) {
            return;
        }
        canRun = false;
        let self = this, args = arguments;
        setTimeout(() => {
            fn.apply(self, args);
            canRun = true;
        }, delay);
    };
}
```

所谓防抖，就好比如果用手指一直按住一个弹簧，它将不会弹起直到松手为止。也就是说预先设定一个执行延迟，当调用动作的时刻大于等于执行延迟，才会执行该动作。若在这执行延迟内又调用此动作则将重新计算执行时间。

```js
function debounce(fn, delay = 300) {
    let timer = null;
    return function() {
        let self = this, args = arguments;
        timer && clearTimeout(timer);
        timer = setTimeout(() => {
            fn.apply(self, args);
        }, delay);
    };
}
```