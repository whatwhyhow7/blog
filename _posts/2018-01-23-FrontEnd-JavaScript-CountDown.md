---
layout: post
title: JavaScript 倒计时的简单实现
tags: [Front-End]
---

**摘要：**倒计时功能很常见，比如说点击发送短信验证码后需要倒计时 60s 后才可以再次发送，再比如说注册成功后倒计时 3s 登录并跳转到指定页面，等等。实现起来也非常简单，但正因为如此，还是有必要记录一下，下次拿来直接用就可以了。
{:.message}

```js
function countDown (time) {
    let self = this;

    if (time === 0) {
        clearTimeout(self.countTime);
        delete self.countTime;
        return;
    } else {
        time--;
    }
    
    self.countTime = setTimeout(function () {
        self.countDown(time);
    }, 1000);
}
```