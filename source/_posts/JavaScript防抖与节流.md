---
title: JavaScript防抖与节流
date: 2020-05-16 20:20:03
tags:
---

# JavaScript防抖与节流函数

防抖与节流是为了处理开发中的性能问题而提出来的概念，通常在滚动事件、浏览器窗口大小变化、频繁点击按钮等场景使用。
<!-- more -->

上面已经说到防抖与节流的使用场景，那具体什么场景下使用防抖什么场景使用节流，还是有区别的。

总结如下：

|概念|使用场景|
|:--|:--|
|防抖|频繁操作，一定时间间隔内只执行最后一次操作|
|节流|频繁操作，间隔一定时间去做一件事|

> 用通俗的话来讲，防抖的原理就是：你尽管触发事件，但是我一定在事件触发 n 秒后才执行，如果你在一个事件触发的 n 秒内又触发了这个事件，那我就以新的事件的时间为准，n 秒后才执行，总之，就是要等你触发完事件 n 秒内不再触发事件，我才执行，真是任性呐!

## 防抖

```js
/**
* fn  执行函数
* wait 等待的时间ms
*/
function debounce(fn, wait) {
    var timeout = null;
    return function() {
        if(timeout !== null) 
            clearTimeout(timeout);
        timeout = setTimeout(fn, wait);
    }
}
```


## 节流

采用时间戳：

```js
 function throttle(func, delay) {
    var prev = Date.now();
    return function() {
        var context = this;
        var args = arguments;
        var now = Date.now();
        if (now - prev >= delay) {
            func.apply(context, args);
            prev = Date.now();
        }
    }
}
```

采用定时器：

```js
function throttle(func, delay) {
    var timer = null;
    return function() {
        var context = this;
        var args = arguments;
        if (!timer) {
            timer = setTimeout(function() {
                func.apply(context, args);
                timer = null;
            }, delay);
        }
    }
}
```

当第一次触发时执行一次事件处理函数，当最后一次时执行一次，可以使用时间戳+定时器的方式，如：

```js
function throttle(func, delay) {
    var timer = null;
    var startTime = Date.now();
    return function() {
        var curTime = Date.now();
        var remaining = delay - (curTime - startTime);
        var context = this;
        var args = arguments;
        clearTimeout(timer);
        if (remaining <= 0) {
              func.apply(context, args);
              startTime = Date.now();
        } else {
              timer = setTimeout(func, remaining);
        }
    }
}
```
