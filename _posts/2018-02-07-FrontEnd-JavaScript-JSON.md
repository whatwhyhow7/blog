---
layout: post
title: JSON.parse 和 JSON.stringify 的兼容实现
tags: [Front-End]
---

**摘要：**IE 和死活不想抛弃 IE 用户的产品经理无疑是前端工程师的噩梦，至少对于我这种半路出家的非佛系前端工程师是这样。这不这两天又做噩梦了，测试同学说线上 IE7 的控制台里有报错，我打开一看显示说 `“JSON”未定义`。不得不说 IE 的错误提示非常简约，你倒是给我说下到底是那个 JSON 没定义啊，你不说我还寻思是后端同学的 json 接口格式不对呢。仔细查了一下，原来 IE8 以下不支持 `window.JSON`，导致代码中使用的 `JSON.parse` 和 `JSON.stringify` 均报错了。嗯，很好，非常好，这样我就不会觉得是我代码的问题了，这个锅 IE 你不得不背。#doge脸# 
{:.message}

这个锅 IE 是背定了，不过对于一个逐步向佛系开发转型的前端工程师来说只有做好 IE 的兼容性才能渡劫成功，毕竟去说服想不开的产品经理舍弃 IE 用户的体验就一定避免不了一场撕逼而大伤元气，暴露了自身技艺的不精是小，耽误了佛系修炼的远大进程可就关乎事大了，所以还是 **好吧算了我来改** 吧。

## 别人的实现

这里说的别人的实现就是：

[https://github.com/douglascrockford/JSON-js/blob/master/json2.js](https://github.com/douglascrockford/JSON-js/blob/master/json2.js)

将上述的 js 下载下来在项目中引入就可以了，使用时还是调用 `JSON.parse`，`JSON.stringify`。

其实人家实现的就挺好，各种情况都想到和处理了，代码自然也很规范。但是我当时不知为什么就是不想引入#傲娇脸#，想想自己写一个还能咋的，于是就写了下面不规范的简单实现。

## 我的实现

先说 `JSON.parse` 的简单实现吧。这个真的是非常简单：`$.parseJSON`。你没有看错，这个 `$` 就是 `jQuery`。

当然，这必须得项目中引用了 `jQuery`。但是 `jQuery` 并没有提供一个类似 `$.stringify` 的实现，差评。

所以，如果项目中引入了 `jQuery` 但是还是要用到 `JSON.stringify` 函数，同时又像我一样莫名其妙地不想引入其他别人的实现时，那么就自己实现吧。

```js
(function () {
    if (typeof window.JSON !== 'object') {
        window.JSON = {
            parse: function (jsonStr) {
                return eval('(' + jsonStr + ')'); 
            },
            stringify: function (jsonObj) {
                let items = [],
                    result = '',
                    type = Object.prototype.toString.call(jsonObj); // 数据类型

                if (type === '[object Array]') {
                    // 数组
                    jsonObj.forEach(function (item) {
                        items.push(window.JSON.stringify(item));
                    });
                    result = '[' + items.join(',') + ']';
                } else if (type === '[object Object]') {
                    // 对象
                    for (let item in jsonObj) {
                        items.push('"' + item + '":' + window.JSON.stringify(jsonObj[item]));
                    }
                    result = '{' + items.join(',') + '}';
                } else if (type === '[object String]') {
                    // 字符串
                    result = '"' + jsonObj.replace(/\\/g, '\\\\').replace(/"/g, '\\"').replace(/[\n\r]/g, '\\n') + '"';
                } else {
                    // 其他数据类型
                    result = jsonObj;
                }
                return result;
            }
        };
    }
})();
```