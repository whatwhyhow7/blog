---
layout: post
title: JavaScript 中的各种 for
tags: [Front-End]
---

**摘要：**这里说的各种 for 是指 JavaScript 中 for 语句的各种衍生语法，比如 `ES5` 的 `forEach` 和 `map`、`jQuery` 的 `each` 等等。看似简单方便的语法命令，但是每到用的时候，就会有些彷徨：`forEach` 到底能不能遍历对象呢？`each` 的回调里 `index` 和 `value` 到底哪个在前？当面临诸如此类非常细微却又永远记不住的细节的时候，我认为就有必要总结一下了，记不住的时候记得来这里查一下。
{:.message}

## for

适用于 **数组**、**计数器**、**字符串** 的遍历。

```js
for (var i = 0; i < array.length; i++) {
    // do something
}
```

## for in

适用于 **对象属性**、**字符串** 的遍历。尽管也可以用来遍历数组，但是不太推荐。

```js
for (var key in object) {
    // do something
}
```

## for of

适用于 **数组**、**对象属性**、**字符串** 的遍历。尽管可以遍历多种数据结构，但是缺点是只有 `ES5` 之后可用。

```js
for (var key of object) {
    // do something
}
```

## forEach

适用于 **数组**  的遍历。优点是写法简单了许多，但是缺点是只有 `ES5` 之后可用，并且不能用 `break` 和 `continue` 中断循环。

```js
array.forEach(function (value, index, arr) {
    // do something
});
```

## map

适用于 **数组**  的遍历。与 `forEach` 类似，不同之处在于 `map` 的回调函数中支持返回值，而 `forEach` 不支持。

```js
array.map(function (value, index, arr) {
    // do something
    return  value + 1;
});
```

## each

适用于 **数组**、**对象属性**  的遍历。依赖于 `jQuery`。

```js
$.each(object, function (index, value, arr) {
    // do something
})
```
