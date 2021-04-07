---
title: JavaScript事件循环与异步原理
date: 2020-05-27 20:23:34
tags:
---


# JavaScript事件循环及异步原理

[[TOC]]

理解JavaScript的事件循环和异步原理，是我们理解所编写的JS执行过程的关键。本文引用了网上一些好文，然后借助自己的理解用通俗的表达进行了阐述。

## 前言

先提出一个问题**JavaScript 既然是单线程，那为什么浏览器或 Node.js 可以执行异步操作呢？**

下面简短解释一下：

> 1、JavaScript 是单线程的，只有一个主线程；
>
> 2、函数内的代码是从上到下依次执行，遇到被调用的函数先进入被调用的函数执行，待完成后继续执行；（这个机制主要是通过函数调用栈实现的）
>
> 3、遇到异步事件，JavaScript 的宿主环境会另开一个线程，主线程继续执行，待结果返回后，执行回调函数。

上述的宿主环境，则是指浏览器或 Node.js 环境，在浏览器中一般会提供额外的线程，而在 Node.js 中，则是借助 libuv 来实现不同操作系统上的多线程。并且在宿主环境中，这个异步线程又分为 __微任务__ 和 __宏任务__ 。

以上内容不明白没关系，接着往下看。

## JavaScript 单线程历史

我们知道，JavaScript 刚出来的时候是作为浏览器内的一种脚本语法，负责操作 DOM，与用户进行互动等，如果是多线程的话，执行顺序无法预知，而且操作以哪个线程为准也是个难题。所以为了避免这种局面，JavaScript 便采用单线程设计，这已经成了这门语言的核心特征，将来也不会改变。

> 在 HTML5 时代，浏览器为了充分发挥 CPU 性能优势，允许 JavaScript 创建多个线程，但是即使能额外创建线程，这些子线程仍然是受到主线程控制，而且不得操作 DOM，类似于开辟一个线程来运算复杂性任务，运算好了通知主线程运算完毕，结果给你，这类似异步的处理方式，但并没有改变 JavaScript 单线程的本质。

## 函数调用栈

JavaScript只有一个主线程，所以也只有一个**函数调用栈**，学过数据结构的同学应该都知道，栈是一种后进先出（LIFO）的数据结构。

在JavaScript中，每当开始执行一个函数时，就会创建一个函数的执行上下文，我们可以笼统的将JavaScript中的执行上下文分为**全局上下文**和**函数执行上下文**。可以通过例子理解，如下代码：
```js
function a(){
    var hello = "Hello";
    var world = "world";
    function b(){
        console.log(hello);
    }
    function c(){
        console.log(world);
    }
    b();
    c();
}

a();
```
函数的出入栈顺序如下图：
![image.png](https://blog-1257256187.cos.ap-chengdu.myqcloud.com/img/20200515191736)

> 如果对函数调用栈还不是很了解，请参考我的另外一篇文章：[JavaScript语言核心概念](/index.php/archives/3/)

从函数调用栈的执行特点中可以知道，栈内后一个函数必须在前一个函数执行完成之后才可以开始执行，如果某一个函数任务需要很长时间才能完成的话，例如网络请求，I/O操作等，后面的函数任务就会一直在等待，那么整个系统的效率就会特别低。于是大家意识到，这些耗时久的任务完全可以先挂起，等主线程上的其他任务执行完之后，再回头将这些挂起的任务继续执行，所以有了**任务队列**的概念。

## 任务队列

我们可以简单的理解为一个函数就是一个任务，基本上可以将任务分为**同步任务**和**异步任务**。

同步任务就是指在主线程上排队执行的任务，只有当前一个任务完成之后后一个才会执行；异步任务则是不进入主线程，而是进入**任务对列**的任务，只有**队列任务**通知了主线程说某个异步任务可以执行了，该任务才会进入主线程执行。

所以，我们思考得知，当执行过程碰到`setTimeout`等异步操作时，会将其交给浏览器或 Node.js 的其他线程进行处理，当达到`setTimeout`指定延迟执行的时间后，才会将回调函数放入任务队列中。

我们可以看一个例子：
```js
function fun() {
    function callback() {
        console.log('执行回调');
    }
    setTimeout(callback, 2000);
    console.log('准备');
}
fun();
```
在**调用栈-异步模块-任务队列**模型中，上述代码的执行过程如下：

第一步，`fun()`函数入栈（我们省略了该代码全局执行上下文入栈步骤）

![](https://blog-1257256187.cos.ap-chengdu.myqcloud.com/img/20200515191743)

第二步，因为`fun()`函数内执行了`setTimeout()`，所以`setTimeout()`入栈，如图：

![](https://blog-1257256187.cos.ap-chengdu.myqcloud.com/img/20200515191747)

第三步，由于`setTimeout()`是异步操作，不属于JavaScript主线程模块内容，所以`setTimeout()`进入异步执行模块执行计时，如图

![](https://blog-1257256187.cos.ap-chengdu.myqcloud.com/img/20200515191753)

第四步，`fun()`函数内的`console.log('准备')`函数进入函数调用栈并执行，所以控制台输出`准备`

![](https://blog-1257256187.cos.ap-chengdu.myqcloud.com/img/20200515191756)

第五步，由于`fun()`函数内部没有其他需要继续执行的函数，所以`fun()`出栈，随后全局上下文也没有需要执行的代码，所以全局上下文也出栈，如图：

![](https://blog-1257256187.cos.ap-chengdu.myqcloud.com/img/20200515191801)

第六步，假如此时刚好`setTimeout()`的两秒计时结束，那么异步模块就会将`setTimeout()`的回调函数放到任务队列里面，因为此时函数调用栈已经空闲，所以任务队列依次将任务函数入栈，如图：

![](https://blog-1257256187.cos.ap-chengdu.myqcloud.com/img/20200515191805)

第七步，进入`callback()`回调中，将`console.log('执行回调')`入栈执行，所以在控制台输出`执行回调`，如图：

![](https://blog-1257256187.cos.ap-chengdu.myqcloud.com/img/20200515191808)

第八步，`callback()`再出栈，整段代码执行结束。

> 上面所说的步数并不是说一定是有8步，目的是让大家有个顺序了解接下来每一步会进行什么内容，理解JavaScript的函数调用执行，异步模块和任务队列之间的关系是最重要的。

那么，这段代码整体的过程就是如图所示，通过这种建立底层模型的方式可以加深大家的理解。趁热打铁，请阅读如下代码，想一想在“**调用栈-异步模块-任务队列**”模型中，是怎么样的一个流程：
```js
setTimeout(() => {
    console.log('1');
}, 32);
setTimeout(() => {
    console.log('2');
}, 30);
function fun() {
    console.log('3');
}
for (var i = 0; i < 100000000; i++) {
    i === 99999999 && console.log('4');
}
fun();
```
代码最终输出的内容顺序是`4 3 2 1`，请思考执行过程。

> 注意一点，就是两个`setTimeout()`都会进入异步模块，这里主要进入了异步模块，这两个函数其实是同时执行的，延迟30ms的先完成，先进入队列（先进先出），延迟32ms的后完成后进入队列，所以最后的顺序是`... 2 1`，即2在1前面。

上述讲到异步模块，在浏览器中，例如 Chrome 浏览器，由 webcore 模块担任开启其他线程角色，其提供了`DOM Binding`、`network`、`timer`子模块，这些都可以理解为异步模块，分别对应DOM处理、Ajax、时间处理函数等API。

而在Node.js中，前言里也说到了，是通过libuv来实现在不同操作系统上统一的线程调度API。

## 宏任务与微任务
前言里说到任务由**宏任务**和**微任务**构成，也被称为`task`和`job`，我们看一张网上的事件循环图：

![](https://blog-1257256187.cos.ap-chengdu.myqcloud.com/img/20200515191815)

其中，`Task Queue`是指宏任务，`Microtask Queue`则是微任务。

宏任务大概包括`主线程代码`、`setTimeout`、`setInterval`、`setImmediate（仅Node.js）`、`requestAnimationFrame（仅浏览器）`、`I/O`、`UI Rendering`；

微任务大概包括`Promise.then/catch/finally`、`process.nextTick（仅Node.js）`、`MutationObserver（仅浏览器）`、`Object.observe(已废弃)`。

事件循环中，当主线程的所有任务（函数）执行结束之后，然后顺序执行微任务队列中的所有微任务，当所有的微任务执行完成后，再执行宏任务队列中的下一个宏任务，当这个宏任务执行完毕，再看微任务队列是否存在微任务，如果存在，则顺序执行所有微任务，一直循环直至所有的任务执行完毕。

> 注意，浏览器在每一次宏任务结束的时候都会进行一次渲染

任务队列的事件循环可以用下图表示：

![](https://blog-1257256187.cos.ap-chengdu.myqcloud.com/img/20200515191820)

分析一段代码：
```html
<script>
  setTimeout(() => {
    console.log(4)
  }, 0);
  new Promise((resolve) => {
    console.log(1);
    for (var i = 0; i < 10000000; i++) {
      i === 9999999 && resolve();
    }
    console.log(2);
  }).then(() => {
    console.log(5);
  });
  console.log(3);
</script>
<script>
  console.log(6)
  new Promise((resolve) => {
    resolve()
  }).then(() => {
    console.log(7);
  });
</script>
```
代码中输出顺序为：`1 2 3 5 6 7 4`；简单分析下：

开始，程序往下走，遇到setTimeout，是异步任务，放到异步模块执行，执行结束的回调进入宏任务队列先暂存着，如图：


![](https://blog-1257256187.cos.ap-chengdu.myqcloud.com/img/20200515191827)

继续往下走，碰到Promise对象。由于是new操作，其构造函数是一个匿名函数，所以会立即执行Promise构造函数的实参函数任务，所以`console.log(1)`被执行，控制台输出1，接着进入循环，直到执行`resolve()`，执行完该函数之后，会附带调用`then`方法，因为`then`属于异步方法，所以`then`内部的回调`console.log(5)`被送入微任务队列，接着执行`console.log(2)`，控制台输出2，此时状态如图：

![](https://blog-1257256187.cos.ap-chengdu.myqcloud.com/img/20200515191831)

程序往下走，紧接着执行`console.log(3)`，所以控制台输出3。到现在控制台输出顺序为1 2 3。

![](https://blog-1257256187.cos.ap-chengdu.myqcloud.com/img/20200515191835)

到这里，第一段脚本里已经结束了，所以此时在这段`<script>`脚本中函数调用栈已空，按照之前的事件循环逻辑，微任务队列里的任务会依次放到函数调用栈里面执行，所以接下来控制台就输出5，如图：

![](https://blog-1257256187.cos.ap-chengdu.myqcloud.com/img/20200515191839)

当微任务队列中的所有任务执行完毕（这里只有一个微任务），函数调用栈为空会先看程序是否可以继续，由于下一个`<script>`脚本存在，所以事件循环被打断，继续下一个脚本内容，所以先执行`console.log(6)`，控制台输出6，此时已输出顺序为`1 2 3 5 6`，如图：

![](https://blog-1257256187.cos.ap-chengdu.myqcloud.com/img/20200515191844)

接下来，又将碰到一个Promise，Promise内构造函数的回调参数函数会立即执行，内部执行到`resolve()`则会调用其`then()`，由于`then()`是异步方法，所以进入异步执行模块执行之后将`console.log(7)`放入微任务队列，如图：

![](https://blog-1257256187.cos.ap-chengdu.myqcloud.com/img/20200515191850)

由于在这个`<script>`脚本里没有其余代码，所以接下来执行所有的微任务，则继续执行`console.log(7)`，随后根据事件循环原理执行下一个宏任务`console.log(4)`，到此所有的代码执行完毕，所以最终的顺序是`1 2 3 5 6 7 4`。

可以尝试分析下下面这个题：
```js
setImmediate(() => {
    console.log(1);
},0);
setTimeout(() => {
    console.log(2);
},0);
new Promise((resolve) => {
    console.log(3);
    resolve();
    console.log(4);
}).then(() => {
    console.log(5);
});
console.log(6);
process.nextTick(()=> {
    console.log(7);
});
console.log(8);
```


## 剩下的疑问
1、如果整体script属于宏任务，那么主线程的函数调用栈算不算入宏任务里面？如果算入，那如下代码是否顺序应该是`1 2 3 5 6 7 8 4`?结果肯定不是，正确顺序是`1 2 3 5 6 8 7 4`;所以笔者觉得在微任务console.log(5)执行结束，即第一次微任务队列被清空，函数调用栈会先判断程序是否还有script代码可以加载，若可以则截断本次事件循环，再次进入顺序执行状态，这样似乎说的通一些。

```html
<script>
  setTimeout(() => {
    console.log(4)
  }, 0);
  new Promise((resolve) => {
    console.log(1);
    for (var i = 0; i < 10000000; i++) {
      i === 9999999 && resolve();
    }
    console.log(2);
  }).then(() => {
    console.log(5);
  });
  console.log(3);
</script>
<script>
  console.log(6)
  new Promise((resolve) => {
    resolve()
  }).then(() => {
    console.log(7);
  });
  console.log(8);
</script>
```

参考文章：

[梁音.JavaScript 事件循环及异步原理（完全指北）](https://www.cnblogs.com/liangyin/p/9783342.html);
代码题目取自该文章，其文章后面最后还有一个进阶题，有兴趣伙伴可以研究下。

[segmentfault-js中宏任务与微任务](https://segmentfault.com/a/1190000020225668)

[博客园-js 宏任务和微任务](https://www.cnblogs.com/wangziye/p/9566454.html)

