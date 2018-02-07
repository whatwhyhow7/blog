---
layout: post
title: JSON.parse 和 JSON.stringify 的兼容实现
tags: [Front-End]
---

**摘要：**IE 和死活不想抛弃 IE 用户的产品经理无疑是前端工程师的噩梦，至少对于我这种半路出家的非佛系前端工程师是这样。这不这两天又做噩梦了，测试同学说线上 IE7 的控制台里有报错，我打开一看显示说 `“JSON”未定义`。不得不说 IE 的错误提示非常简约，你倒是给我说下到底是那个 JSON 没定义啊，你不说我还寻思是后端同学的 json 接口格式不对呢。仔细查了一下，原来 IE8 以下不支持 `window.JSON`，导致代码中使用的 `JSON.parse` 和 `JSON.stringify` 均报错了。嗯，很好，非常好，这样我就不会觉得是我代码的问题了，这个锅 IE 你不得不背。#doge脸# 
{:.message}

这个锅 IE 是背定了，不过对于一个逐步向佛系开发转型的前端工程师来说只有做好 IE 的兼容性才能渡劫成功，毕竟去说服想不开的产品经理舍弃 IE 用户的体验就一定避免不了一场撕逼而大伤元气，暴露了自身技艺的不精是小，耽误了佛系修炼的远大进程可就关乎事大了，还是 好吧算了我来改 吧。

```js
function stringify (json) {
    if (JSON && JSON.stringify) {
        return JSON.stringify(json);
    } else {
        let self = this,
            jsonArray = [];
        $.each(json, function (key, val) {
            let jsonItem = '"' + key + '"' + ':',
                jsonValue = '';
            if ($.isPlainObject(val)) {
                jsonValue = self.stringify(val);
            } else if (Array.isArray(val)) {
                let arrayArray = [];
                val.forEach(function (item) {
                    arrayArray.push($.isPlainObject(item) ? self.stringify(item) : '"' + item + '"');
                });
                jsonValue = '[' + arrayArray.join(",") + ']';
            } else {
                jsonValue = '"' + val + '"';
            }
            jsonItem += jsonValue;
            jsonArray.push(jsonItem);
        });
        return "{" + jsonArray.join(",") + "}";
    }
}
```