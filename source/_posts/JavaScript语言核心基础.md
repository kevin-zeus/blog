---
title: JavaScript语言核心基础
date: 2021-05-28 20:24:17
tags:
---


# JavaScript语言核心基础


## 一、数据结构

在JS语言核心中，我们必须了解三种数据结构：栈（stack）、堆（heap）、队列(queue)。

### 栈

栈是一种**先进后出，后进先出（LIFO）**的数据结构，类似于只有一个出入口的羽毛球盒，在JS中，栈可以用来规定代码的执行顺序，例如**函数调用栈（call stack）**

JS的数组是提供了两个栈操作的方法，

`push`向数组的末尾添加元素(进栈)

`pop`取出数组最末尾的一个元素(出栈)

### 堆

堆是一种树形的结构，JS中的对象表示可以看成是一种堆的结构，如：

```js
var root = {
    a: 10,
    b: 20,
    c: {
        d: 30,
        e: 40
    }
}
```

可以画出堆的图示如：

![image-20190520130628236](http://ww3.sinaimg.cn/large/006tNc79ly1g37p9bklt3j308j06b0sq.jpg)

那么对应的，JS的对象一般是保存在堆内存中。

### 队列

队列是一种**先进先出（FIFO）**的数据结构，类似于排队过安检一样，理解队列对理解JS的事件循环很有帮助

## 二、数据类型和内存空间

### 基础数据类型

最新的ECMAScript标准号定义了7种数据类型，其中包括6种基本数据类型和一种引用类型

其中，基本数据类型是：Number、String、Boolean、Null、Undefined、Symbol (在ES5中没有Symbol类型)，

一种引用数据类型是Object 

> 目前浏览器对Symbol类型的兼容不行，因此建议在实际开发中不使用Symbol

我们在书写函数的时候，声明变量一般是这样的：

```js
function foo(){
    var num1 = 28;
    var num2 = 39;
    ...
}
```

那，在运行函数foo的时候，它的变量保存在哪里？从JS的内存管理上来看，函数运行时，会创建一个执行环境，这个执行环境叫做**执行上下文（EC）**，在执行上下文中，会创建一个变量对象(VO)，即**函数内声明的基础数据类型保存在该执行上下文的变量对象中**。

> 变量对象是保存在堆内存中的，但是由于变量对象具有特殊功能，所以在理解时，我们将变量对象与堆内存空间区分开来

引用数据类型

引用数据类型除了Object，数组对象、正则表达式、函数等也属于引用数据类型。其中，引用数据类型的值是保存在堆内存空间中的对象。如：

```js
var o = {
    a: 10,
   	b: { m: 20}
}
```

对于如上代码，`o`属于引用数据类型，等号右边的内容属于其值，那么`{a:10,b:{m:20}}`存在堆内存空间，o存在对应的执行上下文的变量对象中，这里的执行上下文为全局。

我们根据一个例子和图示理解下：

```js
function foo(){
    var num = 28;
    var str = 'hello';
    var obj = null;
    var b = { m: 20 };
    var c = [1,2,3];
    ...
}
```

如图，当我们想要访问对象b的内容时，实际上是通过一个引用(地址指针)来访问的：

![image-20190520140256862](http://ww1.sinaimg.cn/large/006tNc79ly1g37qw2y9h9j30iw0b3t9a.jpg)

我们再来思考两个问题：

```js
var a = 20;
var b = a;
b = 30;
console.log(a);  // 此时输出多少？
var m = {x:10, y:20};
var n = m;
n.y = 30;
console.log(m.y); // 此时输出多少？
```

输出的结果是20  30，如果能够理解这两个输出，那么相信你对于引用和JS的内存管理是理解了。

### 内存空间管理

JS有自动垃圾回收机制，当一块内存空间的数据能够被访问时，垃圾回收器就认为该数据暂时未使用完不算垃圾，碰到不需要再使用的内存空间数据时，会将其标记为垃圾，并释放该内存空间，也就是**标记-清除算法**。这个算法会从全局对象开始查找，类似从树的根节点往下找，进行标记清除。

所以，一般当一个函数执行完之后，其内部的变量对象就会被回收。但是如果是全局变量的话，变量什么时候释放对于回收器来说是比较难判断的，我们为了性能，应该尽量避免过多的使用全局变量或者在不使用该全局变量时手动设置变量值为null这种方式释放。

## 三、执行上下文

前面说到，JS在执行一个函数时会创建一个执行上下文。其实，在全局环境下也会有执行上下文，可以笼统的将执行上下文分为局部执行上下文和全局执行上下文。

一个JS程序中只能有一个全局环境，但是可以有很多局部环境，所以可见，在一个JS程序中，必定出现多个执行上下文。**JS引擎以函数调用栈的方式来处理执行上下文，其中栈底永远都是全局上下文，栈顶则是当前正在执行的上下文，栈顶的执行上下文执行完毕后，会自动出栈**。

我们通过一个实例来理解：

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

第一步，全局上下文入栈，并置于栈底：

![image-20190520142921427](http://ww2.sinaimg.cn/large/006tNc79ly1g37rnkirisj306y0ah749.jpg)

第二步，全局上下文入栈后，开始执行全局上下文内部代码，直到遇到a()，a()激活了函数a，从而创建了a的执行上下文，于是a的执行上下文入栈，如图：

![image-20190520143147280](http://ww2.sinaimg.cn/large/006tNc79ly1g37rq3oseuj306y09hjre.jpg)

第三步，a的执行上下文执行内容，碰到了b()激活了b函数，所以b的执行上下文入栈：

![image-20190520143403615](http://ww3.sinaimg.cn/large/006tNc79ly1g37rsg36ptj306v09i0ss.jpg)

第四步，在b的执行上下文里面，没有可以生成其他执行上下文的情况，所以这段代码可以顺利执行完毕，b的执行上下文出栈。

![image-20190520143147280](http://ww2.sinaimg.cn/large/006tNc79ly1g37rq3oseuj306y09hjre.jpg)

第五步，b的执行上下文出栈之后，急需执行a的后面内容，碰到了c()激活了c函数，所以c的执行上下文入栈，如图所示：

![image-20190520143859026](http://ww1.sinaimg.cn/large/006tNc79ly1g37rxkhknnj306o09kwej.jpg)

第六步，在c的执行上下文中，没有其他的生成执行上下文内容，所以当c里面的执行代码结束后，c的执行上下文出栈：

![image-20190520144111387](http://ww2.sinaimg.cn/large/006tNc79ly1g37rq3oseuj306y09hjre.jpg)

第七步，a接下来的代码也执行完毕，所以接着a的执行上下文出栈

![image-20190520142921427](http://ww2.sinaimg.cn/large/006tNc79ly1g37rnkirisj306y0ah749.jpg)

最后，全局上下文在浏览器窗口关闭（或Node程序终止）的时候出栈。

**注意**：函数执行中，如果碰到`return`会直接终止可执行代码的执行，因此会直接将当前上下文弹出栈。

总的执行顺序如图：

![image-20190520145341482](http://ww4.sinaimg.cn/large/006tNc79ly1g37scvs047j30wb0aigm4.jpg)

思考下面的程序从执行上下文来看分为几步？

```js
function a(){
    var hello = "hello";
    function b(){
        console.log(b);
    }
    return b;
}
var result = a();
result();
```

图示如下：

![image-20190520145859647](http://ww3.sinaimg.cn/large/006tNc79ly1g37sie58l3j30n90abdg3.jpg)

## 四、变量对象

前面我们提到过变量对象，在JS代码中声明的所有变量都保存在变量对象中，其中变量对象包含如下内容：

1. 函数的所有参数
2. 当前上下文的所有函数声明(通过function声明的函数)
3. 当前上下文的所有变量声明(通过var声明的变量)

### 创建过程

1. 在Chrome浏览器（Node）中，变量对象会首先获取函数的参数变量及值；在Firefox浏览器中，直接将参数对象arguments保存到变量对象中；
2. 先依次获取当前上下文所有的函数声明，也就是function关键字声明的函数。函数名作为变量对象的属性，其属性值为指向该函数所在的内存地址引用。**如果函数名已存在，那么属性值会被新的引用覆盖**。
3. 依次获取当前上下文所有的变量声明，也就是var关键字声明的变量。每找到一个变量就在变量对象中建议一个属性，属性值为undefined。**如果该变量名已存在，为防止同名函数被修改为undefined，则会直接跳过该变量，原属性值不修改**

我们根据上面的过程，思考下面这一句代码执行的过程：

```js
var a = 30;
```

过程如下：

第一步，上下文的创建阶段会先确认变量对象，而变量对象的创建过程对于变量声明来说是先获取变量名并赋值为undefined，所以第一步拆解为：

```js
var a = undefined;
```

上下文创建阶段结束后，进入执行阶段，在执行阶段完成变量赋值的工作，所以第二步是：

```js
a = 30;
```

> 需要注意的是，这两步分别是在上下文的创建阶段和执行阶段完成的，因此var a=undefined是提前到比较早的地方去执行了，也即是变量提升（Hoisting）。所以，我们现在要有意识，就是JS程序的执行是分为上下文创建阶段和执行阶段的

思考如下代码的执行顺序：

```js
console.log(a);  // 输出什么？
var a = 30;
```

> 在变量对象的创建过程中，函数声明的优先级高于变量声明，而且同名的函数会覆盖函数与变量，但是同名的变量并不会覆盖函数。不过在上下文的执行阶段，同名的函数会被变量重新赋值。

如下代码中：

```js
var a = 20;
function fn(){ console.log('函数1') };
function fn(){ console.log('函数2') };
function a(){ console.log('函数a') };


fn();
var fn = '我是变量但是我要覆盖函数';
console.log(fn);
console.log(a);

// 输出：
// 函数2
// 我是变量但是我要覆盖函数
// 20
```

上面例子执行的顺序可以看成：

```js
/** 创建阶段 **/
// 函数变量先提升
function fn(){ console.log('函数1') };
function fn(){ console.log('函数2') };
function a(){ console.log('函数a') };
// 普通变量接着提升
var a = undefined; 
var fn = undefined;  // 创建阶段即使同名，但是变量的值不会覆盖函数值

/** 执行阶段 **/
a = 20;
fn();
fn = '我是变量但是我要覆盖函数';
console.log(fn);
console.log(a);
```

### 实例分析

```js
function foo(){
    console.log(a);
    console.log(fn());
    
    var a = 1;
    function fn(){
        return 2;
    }
}
foo();
```

运行foo函数时，对应的上下文创建，我们使用如下形式来表达这个过程：

```js
/** 创建过程 **/
fooEC(foo的执行上下文) = {
    VO: {},		// 变量对象
    scopeChain: [],		// 作用域链
    this: {}	
}

// 这里暂时不讨论作用域与this对象

// 其中，VO含如下内容
VO = {
    arguments: {...},
    fn: <fn reference>,
    a: undefined
}
```

创建过程中会创建变量对象，所以如上形式所示。在函数调用栈中，如果当前上下文在栈顶，那就开始执行，此时变量对象称为活动对象（AO，Activation Object）：

```js
/** 执行阶段 **/
VO -> AO
AO = {
    arguments: {},
    fn: <fn reference>,
    a: 1
}
```

所以，这段代码的执行顺序应该为：

```js
function foo(){
    function fn(){
    	return 2;
    }
    var a = undefined;
    console.log(a);
    console.log(fn());
    a = 1;
}
foo();
```

### 全局上下文的变量对象

以浏览器为例，全局变量对象为window对象。而在node中，全局变量对象是global。

```js
windowEC = {
    VO: window,
    this: window,
    scopeChain: []
}
```

## 五、作用域与作用域链

在其他的语言中，我们肯定也听说过作用域这个词，**作用域是用来规定变量与函数可访问范围的一套规则**。

### 种类

在JS中，作用域分为全局作用域与函数作用域。

### 全局作用域

全局作用域中声明的变量与函数可以在代码的任何地方被访问。

如何创建全局作用域下的变量：

1. 全局对象拥有的属性与方法

    ```js
    window.alert
    window.console
    ```

2. 在最外层声明的变量与方法

    ```js
    var a = 1;
    function foo(){...}
    ```

    

3. 非严格模式下，不使用关键字声明的变量和方法【不要使用！！】

    ```js
    function foo(){
        a = 1;    // a会成为全局变量
    }
    ```

### 函数作用域

函数作用域中声明的变量与方法，只能被下层子作用域访问，而不能被其他不相干的作用域访问。

例如：

```js
function foo(){
    var a = 1;
    var b = 2;
}
foo();
function sum(){
    return a+b;
}
sum(); // 执行报错，因为sum无法访问到foo作用域下的a和b
```

但是像下面这样写是对的：

```js
function foo(){
    var a = 1;
    var b = 2;
    function sum(){
        return a+b;
    }
    sum();	// 可以访问，因为sum的作用域是foo作用域的子作用域
}
foo();
```

> 在ES6之前，ECMAScript没有块级作用域，因此使用时需要特别注意，一定是在函数环境中才可以生成新的作用域。而ES6之后，我们可以通过用let来声明变量或方法，这样它们就能在"{"和"}"之间形成块级作用域

### 模拟块级作用域

我们可以通过函数来模拟块级作用域，如下：

```js
var arr = [1,2,3,4];

(function(){
    for (var i=0; i< arr.length; i++ ){
        console.log(i);
    }
})();

console.log(i); // 输出undefined，因为i在函数作用域里
```

这种函数叫做**立即执行函数**

写法大致有如下几种，建议第一种写法：

```js
(function(){
    ...
})();

!function(){
    ...
}();
    
// 把!改成+或-也可以
```

> 在ECMAScript开发中，我们可能会经常使用立即执行函数方式来实现模块化。

### 作用域链

```js
function a(){
    ...
    function b(){
        ...
    }
}
a();
```

如上伪代码中，先后创建了全局函数a和函数b的执行上下文，假设加上全局上下文，它们的变量对象是VO(global)，VO(a)和VO(b)，那么b的作用域链则同时包含了这三个变量对象，如下：

```js
bEC = {
    VO: {...},
    scopeChain: [VO(b), VO(a), VO(global)],  //作用域
    this: {}
}
```

> 作用域链是在代码执行阶段创建的，理解作用域链是学习闭包的前提，闭包里面会有更多的对作用域链的应用

## 六、闭包

### 什么是闭包

简单来讲的话，闭包就是指有权访问另一个函数作用域中变量的函数，其本质是在函数外部保持了内部变量的引用，。

创建一个闭包最常见的方式，就是在一个函数内部创建另一个函数，这个函数就是闭包，如下：

```js
function foo(){
    var a = 1;
    var b = 2;
    
    function closure(){
        return a + b;
    }
    
    return closure;
}
```

上面的代码中，closure函数就是一个闭包(在chrome的调试里面，用闭包的父作用域函数名表示)，闭包的作用域为`[VO(closure), VO(foo), VO(global)]`

根据上面的理解，我们来看一个例子，里面有闭包吗：

```js
var name = 'window';
var obj = {
    name: 'my object',
    getName: function(){
        return function(){
            return this.name
        }
    }
}
console.log( obj.getName()() )  // 输出： window
```

在这个例子中，虽然在getName函数里面，用了一个内部函数，但是我们发现最终返回的this.name输出window，可以看出这不是一个闭包。因为其返回的是个**匿名函数**，而**匿名函数的执行上下文是全局上下文**，因此其this对象通常指向全局对象，所以this.name输出了window。那我们怎么修改可以让其返回obj的name属性呢？

如下：

```js
var name = 'window';
var obj = {
    name: 'my object',
    getName: function(){
        var _this = this;
        return function(){
            return _this.name
        }
    }
}
console.log( obj.getName()() )  // 输出： my object
```

> 总结下，就是闭包的作用域链必须是包含了他的父级函数作用域，使用了父级作用域的变量对象下的变量肯定就包含了父级作用域

### 闭包和垃圾回收机制

我们来回顾下垃圾回收机制：当一个值不再被引用时会被标记然后清除，当一个函数的执行上下文运行完毕后，内部所有的内容都会失去引用而被清除。

闭包的本质是保持在外部对函数的引用，所以闭包会阻止垃圾回收机制进行回收。

例如以下代码：

```js
function foo(){
    var n = 1;
    nAdd = function(){
        n += 1;
    }
    return function fn(){
        console.log(n);
    }
}
var result = foo();
result();      // 1
nAdd();
result();      // 2
```

因为nAdd和fn函数都访问了foo的n变量，所以它们都与foo形成了闭包。这个时候变量n的引用被保存了下来。

所以，**在使用闭包时应该警惕，滥用闭包，很可能会因为内存原因导致程序性能过差**

### 闭包的应用场景

> 回顾下，使用闭包后，任何在函数中定义的变量，都可以认为是私有变量，因为不能在函数外部访问这些变量。私有变量包括函数的参数、局部变量和函数定义的其他函数。

#### 循环、setTimeout与闭包

我们先来看一个面试常见的例子：

```js
for( var i=0; i<5; i++ ){
    setTimeout(function timer(){
        console.log(i);
    }, i*1000);
}
```

可能乍一看会觉得每隔1秒从0输出到4，但是实际的运行是每隔1秒输出一个5。

我们来分析一下：

1. for循环不能形成自己的作用域，所以i是全局变量，会随着循环递增，循环结束后为5
2. 在每一次循环中，setTimeout的第二个参数访问的都是当前的i，因此第二个参数中i分别为0，1，2，3，4
3. 第一个参数timer访问的是timer函数执行时的i，由于延迟原因，当timer开始执行时，此时i已经为5了

如果我们要隔秒输出0,1,2,3,4，那就需要让for循环形成自己的作用域，所以需要借助闭包的特性，将每一个i值用一个闭包保存起来。如下代码：

```js
for( var i=0; i<5; i++ ){
    (function(i){
        setTimeout(function timer(){
            console.log(i);
        }, i*1000);
    })(i);
}
```

当然，在ES6或更高版本中，可以直接使用let关键字形成for的块级作用域，这样也是OK的：

```js
for( let i=0; i<5; i++ ){
    setTimeout(function timer(){
        console.log(i);
    }, i*1000);
}
```

#### 单例模式与闭包

JavaScript也有许多解决特定问题的编码思维(设计模式)，例如我们常听到过的工厂模式、订阅通知模式、装饰模式、单例模式等。其中，单例模式是最常用也是最简单的一种，我们尝试用闭包来实现它。

其实在JS中，**对象字面量就是一个单例对象**。如下：

```js
var student = {
    name: 'zeus',
    age: 18,
    getName: function(){
        return this.name;
    },
    getAge: function(){
        return this.age;
    }
}
student.getName();
student.name;
```

但是，这种对象的变量很容易被外部修改，不符合我们的需求，我们期望建立自己的私有属性和方法。如下：

```js
var student = (function(){
    var name = 'zeus';
    var age = 18;
    
    return {  // 外部可访问内容
        getName: function(){
            return name;
        },
        getAge: function(){
            return age;
        }
    }
})();
student.getName();
student.name;  // undefined
```

如上，第二个例子中，在立即函数执行的时候就返回student对象了，下面我们写一个例子，在调用时才初始化：

```js
var student = (function(){
    var name = 'zeus';
    var age = 18;

    var instance = null; // 定义一个变量，用来保存实例
    
    function init(){
        return {
            getName: function(){
                return name;
            },
            getAge: function(){
                return age;
            }
        }
    }

    return {
        getInstance: function(){
            if ( !instance ){
                instance = init();
            }
            return instance;
        }
    }

    
})();
var student1 = student.getInstance();
var student2 = student.getInstance();
console.log( student1 === student2 );  // true
```

#### 模块化与闭包

提出一个问题：如果想在所有的地方都能访问同一个变量，应该怎么做？例如全局的动态管理。

解决方案：使用全局变量(但是时间开发中，不建议轻易使用全局变量)。

其实，模块化的思想可以帮助我们解决这个问题。

> 模块化开发是目前最流行，也是必须要掌握的一种开发思路。而模块化其实是建立在单例模式上的，因此模块化开发和闭包息息相关。目前比如Node里的require，ES6的import和modules等，实现方式不同但是核心思路是一样的

模块化架构一般需要实现下面三个内容：

1.**每一个单例就是一个模块，在当前的一些模块化开发中，每一个文件是一个模块**

2.**每个模块必须有获取其他模块的能力**

如在一些模块化开发中，使用require或者import来获取其他模块内容

3.**每一个模块都应该有对外的接口，以保证与其他模块交互的能力**

在一些模块化开发中使用module.exports或者export default {}等将允许其他模块使用的接口暴露出来

我们今天使用单例模式，来实现简单的模块化思想，案例实现的是每隔一秒，body的背景色就随着一个数字的递增在固定的三个颜色之间切换：

```js
/**
*   管理全局状态模块，含有私有变量并暴露两个方法来获取和设置其内部私有变量
*/
var module_status = (function(){
    var status = {
        number: 0,
        color: null
    }

    var get = function(prop){
        return status[prop];
    }

    var set = function(prop, value){
        status[prop] = value;
    }

    return {
        get: get,
        set: set
    }
})();
/**
*   负责body背景颜色改变的模块
*/
var module_color = (function(){
    // 假装用这种方式执行第二步引用模块
    var state = module_status;

    var colors = ['#c31a86', 'orange', '#ccc'];

    function render(){
        var color = colors[ state.get('number') % 3];
        document.body.style.backgroundColor = color;
    }

    return {
        render: render
    }

})();
/**
*   负责显示当前number值模块，用于参考对比
*/
var module_context = (function(){
    var state = module_status;

    function render(){
        document.body.innerHTML = 'this Number is '+state.get('number');
    }

    return {
        render: render
    }

})();
/**
*   主模块，借助上面的功能模块实现我们需要的功能
*/
var module_main = (function(){
    var state = module_status;
    var color = module_color;
    var context = module_context;

    setInterval(function(){
        var newNumber = state.get('number') + 1;
        state.set('number', newNumber);

        color.render();
        context.render();
    }, 1000);
})();
```

> 自己分析整个完整的代码之后，真的很有帮助

## 七、this对象

> 上面六大节的内容，可以算是JavaScript的进阶，但其实应该算是JavaScript的基础，具备这些知识的时候再来看this对象这一节，收获会很大。在JS中，最重要的部分就是理解闭包和this！

我们来回顾下执行上下文和变量对象那一节，我们知道在函数被调用执行时，变量对象VO会生成，这个时候，this的指向会确定。因此，必须牢记**当前函数的this是在函数被调用执行的时候才确定的**，也就是说this对象需要当前执行上下文在函数调用栈的栈顶时，VO变成AO，同时this的指向确定。

如下代码：

```js
var a = 10;
var obj = {
    a: 20
}
function fn(){
    console.log(this.a);
}
fn();  // 10
fn.call(obj); // 20
```

代码里面，fn函数里的this分别指向了window(全局对象变量)与obj

### 全局对象的this

全局对象的变量对象是一个比较特殊的存在，在全局对象中，this指向它本身，所以比较简单

```js
this.a1 = 10;
var a2 = 20;
a3 = 30;

console.log(a1); //10
console.log(a2); //20
console.log(a3); //30
```

以上的用法都会构建全局变量，且在非严格模式语法上没有错误。

### 函数中的this

在本节第一个例子中，我们看到，同一个函数的this由于调用方式不同导致this的指向不同，因此，在函数中，this最终指向谁，与调用该函数的方式有关。

**在一个函数的执行上下文中，this由该函数的调用者提供，由调用函数的方式来决定其指向**

如下例子：

```js
function fn(){
    console.log(this);
}
fn();	// fn为调用者，独立调用，非函数的严格模式下指向全局对象window
```

**如果调用者被某个对象拥有，那么在调用该函数时，函数内部的this指向该对象。如果调用者函数独立调用，那么该函数内部this指向undefined，但是在非严格模式下，当this指向undefined时，它会指向全局对象。**

```js
function fn(){
    'use strict';
    console.log(this);
}
fn();   // undefined
window.fn();  // window
```

思考一下，如下这个例子返回什么：

```js
var a = 20;
var obj = {
    a: 30
}
function fn(){
    console.log('fn this:', this);
    function foo(){
        console.log(this.a);
    }
    foo();
}
fn.call(obj);
fn();
```

> 另外，对象字面量不会产生作用域，所以如下
>
> ```js
> 'use strict';
> var obj = {
>     a: 1,
>     b: this.a+1
> }
> ```
>
> 严格模式下会报语法错误，非严格模式下this指向全局对象
>
> 思考下面的例子：
>
> ```js
> var a = 10;
> var foo = {
>     a: 20,
>     getA: function(){
>         return this.a;
>     }
> }
> 
> console.log( foo.getA() );  // 20
> 
> var test = foo.getA();
> console.log( test() );	// 10，这里为什么是10?
> ```
>
> 因为test在执行时，test是调用者，它是独立调用，在非严格模式下，其this指向全局对象

思考如下代码输出什么：

```js
function foo(){
    console.log(this.a);
}

function active(fn){
    fn();
}

var a = 20;
var obj = {
    a: 10,
    getA: foo,
    active: active
}

active(obj.getA);
obj.active(obj.getA);
```

### call/apply/bind显式的指定this

JS内部提供了一种可以手动设置函数内部this指向的方式，就是call/apply/bind。所有的函数都可以调用这三个方法。

看如下例子：

```js
var a = 20;
var obj = {
    a: 30
}
function foo(num1,num2){
    console.log(this.a+num1+num2);
}
```

我们知道，直接调用foo(10,10)的话会打印40，如果我们想把obj里的a打印出来，我们像下面这样写：

```js
foo.call(obj,10,10);	// 50
// 或
foo.apply(obj, [10,10]);  // 50
```

那其实call/apply表示将第一个参数作为该函数执行的this对象指向，然后立即执行函数。

call和apply有一点区别，就是传参的区别:

在call中，第一个参数是函数内部this的指向，后续参数则是函数执行时所需参数；

在apply中，只有两个参数，第一个参数是函数内部this的指向，第二个参数是一个数组，数组里面是函数执行所需参数。

bind方法用法与call方法一样，与call唯一不同的是，bind不会立即执行函数，而是直接返回一个新的函数，并且新的函数内部this指向bind方法的第一个参数

## 八、函数与函数式编程

> 其实，我们仔细回顾下会发现，前面的一到七节的内容基本上都是在围绕函数展开的，让我们更加清晰的认识函数，这一节主要了解如何运用函数

### 函数

函数的形式有四种：函数声明、函数表达式、匿名函数与立即执行函数。

#### 1.函数声明

关键字function，从前面的执行上下文创建过程我们知道function关键字声明的变量比var关键字声明的变量有更高的优先执行顺序，所以变量提升中，先提升函数变量。

```js
function fn(){ ... }
```

#### 2.函数表达式

指将一个函数体赋值给一个变量的过程

```js
var fn = function(){ ... }
```

可以理解为：

```js
// 创建阶段
var fn = undefined;
// 执行阶段
fn = function(){ ... }
```

所以使用函数表达式时，必须要考虑函数使用的先后顺序：

```js
fn();  // TypeError: fn is not a function

var fn = function(){ console.log('hello') }
```

> 请问，如果在函数表达式里面有this，那这个this指向什么？

#### 3.匿名函数

就是指没有名字的函数，一般会作为参数或返回值来使用，通常不使用变量来保存它的引用。

> 匿名函数不一定就是闭包，匿名函数可以作为普通函数来理解，而闭包的形成条件，仅仅是有的时候或者匿名函数有关而已

#### 4.立即执行函数

立即执行函数是匿名函数一个非常重要的应用场景，因为函数可以产生作用域，所以我们经常使用立即执行函数来模拟块级作用域，并进一步在此基础上实现模块化的运用。

### 函数式编程

函数式编程其实就是将一些功能、逻辑等封装起来以便使用，减少重复编码量。函数式编程的内涵就是函数封装思想。怎么去封装，学习前辈优秀的封装习惯。让自己的代码看上去更加专业可靠是我们学习的目的。

#### 1.函数是一等公民

一等公民也就是说函数跟其他的变量一样，没有什么特殊的，我们可以像对待任何数据类型一样对待函数。

- 把函数赋值给一个变量

    ```js
    var fn = function(){}
    ```

- 把函数作为形参

    ```js
    function foo(a, b, callback){
        callback(a+b);
    }
    function fn(res){
        console.log(res);
    }
    foo(2,3,fn);  // 5
    ```

- 函数作为返回值

    ```js
    function foo(x){
        return function(y){
            console.log(x+y);
        }
    }
    foo(2)(3);	// 5
    ```

#### 2.纯函数

相同的输入总会得到相同的值，并且不会产生副作用的函数，叫做纯函数。

例如我们想封装一个获取数组最后一项的方法，有两种选择：

```js
// 第一种
function getLast1(arr){
    return arr[arr.length];
}

// 第二种
function getLast2(arr){
    return arr.pop();
}
```

getLast1和getLast2虽然都可以满足需求，但是getLast2在使用之后会改变arr数组内容，下一次再使用的话，由于arr最后一个值已经被取出，导致第二次使用取到的值是原来值的倒数第二个值。所以，像第二种这样的封装是非常糟糕的，会将原数据弄得特别混乱。在JavaScript的标准函数里，也有许多不纯的方法，我们在使用时要多注意。

#### 3.高阶函数

可以粗暴的理解，凡是接收一个函数作为参数的函数，就是高阶函数。但是这样就太广义了，高阶函数其实是一个高度封装的过程，

我们来尝试封装一个方法`mapArray(array, fn)`，其有两个参数，第一个参数是一个数组，第二个参数是一个函数，其中第二个参数参数有两个参数`fn(item, index)`第一个item表示是数组每次遍历的值，第二个是每次遍历的序号。

```js
var a = [1,2,3,4,5];

function mapArray(array, fn){
    var temp = [];
    if ( typeof fn === "function" ){
        for ( var k=0; k<array.length; k++ ){
            temp.push( fn.call(array, array[k], k) );
        }
    } else {
        console.error('TypeError' + fn + ' is not a function.');
    }
    return temp;
}

var b = mapArray(a, function(item, index){
    console.log(this.length);  // 5
    return item + 3;
});

console.log(b);  // [4,5,6,7,8]
```

`mapArray`函数实现了将数组里的每一项都进行了相同的操作，并且在第二个函数里的this指向的是第一个数组参数对象。

> 从这个封装函数来看，其实是把数组的循环给封装了，那就是说，我们要封装的就是程序公用的那一部分，而具体要做什么事情，则以一个参数的形式，来让使用者自定义。这个被当做参数传入的函数就是基础函数，而我们封装的mapArray方法，就可以称之为高阶函数

**高阶函数其实是一个封装公共逻辑的过程**

#### 4.柯里化函数

> 留个坑，有时间再写

## 九、面向对象

> 虽然JS是面向对象的高级语言，但是它与Java等一类语言不同，在ES6之前是没有class的概念的，基于原型的构建让大家深入理解JavaScript的面向对象有点困难。难点就是重点，所以JS的面向对象肯定是需要我们去了解的

在EcmaScript-262中，JS对象被定义为**无序属性的集合，其属性可以包含基本值、对象或者函数**。

### 对象字面量

从上面的定义中，对象是由一系列的key-value对组成，其中value为基本数据类型或对象，数组，函数等。像这种形式的对象定义格式，叫做对象字面量，如：

```js
var Student = {
    name: 'ZEUS',
    age: 18,
    getName: function(){
        return this.name;
    }
    parent: {}
}
```

### 创建对象

第一种，通过关键字new来创建一个对象：

```js
var obj = new Object();		// new 后面接的是构造函数
```

第二种，使用对象字面量：

```js
var obj = {};
```

我们要给对象创建属性或方法时，可以像这样：

```js
// 第一种方式
var person = {};
person.name = 'zeus';
person.getName = function(){
    return this.name;
}

// 第二种方式
var person = {
    name: 'zeus',
    getName: function(){
        return this.name;
    }
}
```

访问对象的方法或属性，可以使用`.`或者 `['']`

### 构造函数与原型

> 在函数式编程那一节，我们讲到封装函数就是封装一些公共逻辑与功能。当面对具有同一类事物时，我们也可以借助构造函数与原型的方式，将这类事物封装成对象

例如：

```js
var Student = function(name, age){
    this.name = name;
    this.age = age;
    console.log(this);
}
Student.prototype.getName = function(){
    return this.name;
}

// 实例化对象时
var zeus = new Student('zeus', 18);  // zeus实例
zeus.getName();
Student('zeus', 18);   // window
```

可以看到，具体的某个学生的特定属性，通常放在构造函数中；所有学生的方法和属性，通常放在原型对象中。

上述代码输出内容如下图：

![image-20190531113004351](http://ww1.sinaimg.cn/large/006tNc79ly1g3kcagotlnj30g302ojrl.jpg)

> 这里提个问，构造函数是高阶函数吗？在这里，new Student()内部的this为什么会指向实例对象呢，而Student()内部this指向window？

构造函数名约定首字母大写，这里必须要注意。构造函数的this与原型方法中的this指向的都是当前实例。像上面，使用了new关键字之后，Student()函数才是构造函数。那new关键字具体做了什么呢？我们可以来用一个函数模拟new关键字的能力：

```js
function New(func){
    var res = {};
    if ( func.prototype !== null ){
        res.__proto__ = func.prototype; 
    }
    var ret = func.apply(res, Array.prototype.slice.call(arguments, 1) );
    // 当我们在构造函数中明确指定了返回对象时，进行这一步
    if ( (typeof ret ==="object" || typeof ret==="function" ) && ret !== null ){
        return ret;
    }
    // 如果没有明确指定返回对象，则默认返回res，这个res就是实例对象
    return res;
}
```

通过对New方法的封装，可以知道new关键字在创建实例时经历了如下过程：

1. 先创建一个新的、空的实例对象；
2. 将实例对象的原型(\_\_proto\_\_)，指向构造函数的原型(prototype)；
3. 将构造函数内部的this，修改为指向实例；
4. 最后返回该实例对象

![构造函数、原型、实例之间的关系](http://ww1.sinaimg.cn/large/006tNc79ly1g3kd22izbkj30j30cqt98.jpg)

> 我们可不可以在构造函数里面创建方法？当然是可以的，但是这样比较消耗更多的内存空间，因为每一次创建实例对象，都会创建一次该方法。

所以可以看出，在构造函数里声明的变量与方法只属于当前实例，因此我们可以将构造函数中声明的属性与方法看做该实例的私有属性和方法，它们只能被当前实例访问。而原型中的属性与方法能够被所有的实例访问，因此可以将原型中声明的属性和方法称为公有属性与方法。**如果构造函数里的私有属性/方法与原型里的公有属性/方法重名，那么会优先访问私有属性/方法**

> **怎么判断一个对象是否拥有某一个方法/属性**
>
> 1. 通过`in`运算符来判断，无论该方法/属性是否公有，只要存在就返回true，否则返回false
> 2. 通过`hasOwnProperty`方法来判断，只有该方法/属性存在且为私有时，才返回true，否则返回false
>
> ```js
> var Student = function(name, age){
> this.name = name;
> this.age = age;
> this.speak = function(){
>    console.log('我是'+this.name+'的私有方法')
> }
> }
> 
> Student.prototype.getName = function(){
> console.log(this.name);
> }
> 
> var Bob = new Student('Bob', 18);
> Bob.speak();
> Bob.getName();
> 
> console.log( 'speak' in Bob);  // true
> console.log( 'getName' in Bob);  // false
> console.log( Bob.hasOwnProperty('speak') );  // true
> console.log( Bob.hasOwnProperty('getName') );  // false
> ```

如果要在原型上添加多个方法，还可以这样写：

```js
function Student(){};
Student.prototype = {
    constructor: Student,    // 必须声明
    getName: function(){},
    getAge: function(){}
}
```

### 原型对象

原型对象其实也是普通对象。在JS中，几乎所有的对象都可以是原型对象，也可以是实例对象，还可以是构造函数，甚至身兼数职。当一个对象身兼多职时，它就可以被看作原型链中的一个节点。

当要判断一个对象student是否是构造函数Student的实例时，可以使用`instanceof`关键字，其返回一个boolean值:

```js
student instanceof Student;    // true or false
```

我们回到最开始的时候，当创建一个对象时，除了使用对象字面量也可以使用new Object()来创建，因此Object其实是一个构造函数，而其对应的原型Object.prototype则是原型链的终点。

当创建函数时，除了使用function关键字外，还可以使用Function对象：

```js
var add = new Function("a", "b", "return a+b");
// 等价于
var add = function(a, b){
    return a+b;
}
```

在这里，add方法是一个实例对象，它对应的构造函数是Function，它的原型是Function.prototype，也就是`add.__proto__ === Function.prototype`。这里比较特殊的是，Function同时是Function.prototype的构造函数与实例(因为Function也是一个函数啦！)；而与此同时，因为Function是继承自Object的，所以Function.prototype还是Object.prototype的实例，它们的原型链可以用下图表示：

![add函数相关的原型链](http://ww2.sinaimg.cn/large/006tNc79ly1g3klacdklpj30ut0ftjsa.jpg)

对原型链上的方法与属性的访问，与作用域链相似，也是一个单向的查找过程，虽然add与Object原型没有直接关系，但是它们在同一条原型链上，因此add也可以使用Object的toString方法等(比如hasOwnProperty方法)。

### 实例方法，原型方法，静态方法

看如下代码即可了解：

```js
function Foo(){
    this.bar = function(){     // 实例（私有）方法
        return 'bar in Foo';    
    }
}

Foo.bar = function(){		// 静态方法，不需要实例化，直接可以用函数名调用
    return 'bar in static';	
}

Foo.prototype.bar = function(){		// 原型方法
    return 'bar in prototype';
}
```

### 继承

因为封装一个对象是由构造函数与原型共同组成的，所以继承也被分为两部分，一部分是构造函数继承另一部分是原型继承。

如下代码：

```js
var Person = function(name, age){
    this.name = name;
    this.age = age;
}

Person.prototype.say = function(){
    console.log('您好');
}

var Student = function(name, age, grade){
    Person.call(this, name, age);  // 在这里是构造继承
    this.grade = grade;
}
// 下面这两句是原型继承
Student.prototype = new Person();
Student.prototype.constructor = Student;  // 这句一定不能少
Student.prototype.speak = function(){
    console.log(`我叫${this.name}，我今年${this.age}岁了，我语文考了${this.grade}分`);
}

var kevin = new Student('kevin', 18, 90);
kevin.speak();
kevin.say();
```

这段代码属于组合继承，是比较常用的一种继承方式，不过他有个不足就是，无论什么情况下都会调用两次父级构造函数。

如下是优化之后的代码：

```js
function inheritPrototype(child, parent){
    var obj = Object(parent.prototype);
    obj.prototype = child;
    child.prototype = obj;
}

var Person = function(name, age){
    this.name = name;
    this.age = age;
}
Person.prototype.say = function(){
    console.log('您好');
}

var Student = function(name, age, grade){
    Person.call(this, name, age);
    this.grade = grade;
}
inheritPrototype(Student, Person);
Student.prototype.speak = function(){
    console.log(`我叫${this.name}，我今年${this.age}岁了，我语文考了${this.grade}分`);
}

var kevin = new Student('kevin', 18, 90);
kevin.speak();
kevin.say();
```

这一段是寄生组合式继承，是开发人员认为的引用类型最理想的继承方式。

## 十、ES6基础

> ES6是ECMAScript6的简称，也被称为ECMAScript2015。是目前兼容性比较乐观且比较新的ECMAScript标准，虽然增加了前端的学习成本，但是与ES5相比，它提供了很多新的特性，而且现在前端基本上都在转ES6了，所以ES6也是学习前端的必备基础。不过目前，并不是所有的浏览器都支持ES6新特性，但是在开发中，我们可以借助babel提供的编译工具，将ES6转化为ES5，这也极大的推动了前端团队对ES6的接受。对于大多数常用的ES6新特性，目前最新版的Chrome都已全部支持。不过对于部分知识，例如模块化modules，则需要通过构建工具才能够使用，例如使用webpack和babel的VueJS。

### 新的变量声明方式let/const

在ES6中，我们可以使用let来声明变量，其中，let会产生变量的块级作用域，并且let不会进行变量提升，所以这样使用会直接报错：

```js
console.log(a);  // 不会输出undefined，会直接报ReferenceError
let a = 10;
```

所以，如果你决定用ES6的变量声明来写了，就全部用let吧，不要let和var混用。

const是用来声明一个常量的，该常量的引用地址不可改变。

这里需要注意的是let和const变量的值，都是一个引用，如果对let的变量进行赋值操作，是新建了该值之后将其引用重新赋给变量。

例如：

```js
const a = [];
a.push(1);    // 不会报错
const b = 1;
b = 2;   // 报错Uncaught TypeError: Assignment to constant variable.
```

### 箭头函数

ES6的箭头函数是一个用起来比较舒适的方式，我们用例子来看一下：

```js
// ES5中声明函数
var fn = function(a, b){
    return a+b;
}
// ES6箭头函数
var fn = (a, b) => a+b;  // 当函数直接return时，可以省略{}
```

需要注意的是，**箭头函数只能替换函数表达式**，使用function关键字声明的函数不能使用箭头函数替换，如下形式不能用箭头函数替换：

```js
function fn(a,b){
    return a+b;
}
// 不可以替换成下面形式
fn(a, b) => a+b;
```

我们一看到函数就应该去想以下它内部的this在调用时指向谁，从前面知识我们知道，函数内部的this指向，与它的调用者有关，或者使用call/apply/bind也可以修改函数内部的this指向。

我们来回顾一下，请思考下面的输出内容：

```js
var name = 'Tom';
var getName = function(){
    console.log(this.name);   
}
var person = {
    name: 'Alex',
    getName: function(){
        console.log(this.name);
    }
}
var other = {
    name: 'Jone'
}
getName();    // ?
person.getName();   // ?
getName.call(other);    // ?
```

上面分别输出了Tom，Alex，Jone，第一个getName()独立调用，其this指向undefined并自动转向window。那假如全部换成箭头函数呢？我们看一下输出结果：

```js
var name = 'Tom';
var getName = () => {
    console.log(this.name);   
}
var person = {
    name: 'Alex',
    getName: () => {
        console.log(this.name);
    }
}
var other = {
    name: 'Jone'
}
getName();          //Tom
person.getName();   //Tom
getName.call(other);//Tom
```

运行发现，三次都输出了Tom，这也是需要大家注意的地方。**箭头函数中的this，就是声明函数时所处的上下文中的this，他不会被其他方式所改变**。

所以有些场景可以用箭头函数来解决：

```js
document.name = 'doc';
var obj = {
    name: 'zeus',
    do: function(){
        document.onclick = function(){
            console.log(this.name);   // 因为是document调用了该函数，所以点击页面输出doc
        }
    }
}
obj.do();
```

如果我们要在页面被点击后输出zeus，可能最常用的就是在document.onclick外面使用_this/that暂存this的值，如下：

```js
document.name = 'doc';
var obj = {
    name: 'zeus',
    do: function(){
        var _this = this;
        document.onclick = function(){
            console.log(_this.name);   // 使用了_this中间变量，输出zeus
        }
    }
}
obj.do();
```

其实，可以用箭头函数的特性来做：

```js
document.name = 'doc';
var obj = {
    name: 'zeus',
    do: function(){
        document.onclick = () => {
            console.log(this.name);   // 箭头函数的this指向当前上下文
        }
    }
}
obj.do();
```

### 模板字符串

模板字符串是解决一般的字符串拼接麻烦的问题产生的，它使用反引号`包裹字符串，使用${}包裹变量名，如下代码：

```js
// ES5
var a = 'hello';
var b = 'zeus';
var c = 10;
var s = a + ' '+ b + ' ' + (c+10);  // hello zeus 20
// ES6
var str = `${a} ${b} ${c+10}`;   // hello zeus 20
```

### 解析结构

解析结构可以很方便的从数组或对象获取值，例如对于如下的对象字面量：

```js
let zeus = {
    name: 'zeus',
    age: 20,
    job: 'Front-end Engineer'
}
```

如果要取值，我们经常会使用点运算符进行取值，例如`zeus.name`或`zeus['age']`，当使用解析结构时，可以这样做：

```js
const {name, age, job} = zeus;
console.log(name);
```

当然const表示获得到的值声明为常量，也可以使用let或var。我们还可以给属性变量指定默认值:

```js
const {name = 'kevin', age = 20, job = 'student'} = zeus;
// 如果zeus对象对应属性没有值，则使用前面指定的默认值
```

或者给属性变量重新命名：

```js
const {name: username, age, job} = zeus;
// 后面使用的话就必须使用username
```

数组也可以使用解析结构，如下：

```js
let arr = [1,2,3,4];
const [a,b,c,d] = arr;
console.log(a);  // 1
console.log(c);  // 3
```

数组的解析结构的属性变量名可以随意命名，但是是按顺序来一一对应的，而对象解析结构中的属性变量必须跟变量属性命名一致。对象属性的解析结构也可以进行嵌套，例如：

```js
let kevin = {
    name: 'kevin',
    age: 20,
    job: 'Student',
    school: {
    	name: 'smu',
    	addr: '成都'
	}
};
const {school: {name}} = kevin;
console.log(name);  // smu
```

### 展开运算符

在ES6中，使用`...`作为展开运算符，它可以展开数组/对象。例如：

```js
const arr1 = [1,2,3];
const arr2 = [...arr1, 4,5,6];   // [1,2,3,4,5,6]
let person_kevin = {
    name: 'kevin',
    age: 20,
    job: 'Student'
};
let student_kevin = {
    ...person_kevin,
    school: {
        name: 'smu',
        addr: '成都'
    }
};
```

展开运算符可以用在函数形参里面，但是**只能作为函数的最后一个参数**

### Promise

#### 1、异步与同步

同步是指发送一个请求，需要等待直到请求结果返回之后，再继续下一步操作。异步在发送请求后，不会等待而是直接继续下一步操作。

我们来实现一个异步方法：

```js
function fn(){
    return new Promise((resolve, rejsct) =>{
        setTimeout(function(){
            resolve('执行fn内容');
        },1000);
    });
}
```

可以使用async/await来模拟同步效果：

```js
var foo1 = async function(){
    let t = await fn();
    console.log(t);
    console.log('接着执行的内容');
}
foo1();
// 等待1秒后，输出：
// 执行fn内容
// 接着执行的内容
```

如果采用异步操作的话，如下：

```js
var foo2 = function(){
    fn().then(res=>{
        console.log(res);
    });
    console.log('接着执行的内容');
}
foo2();
// 先输出 接着执行的内容
// 等待1秒后
// 输出 执行fn的内容
```

#### 2、简单用法

我们应该有使用过jquery的$.ajax()方法，该方法获取后端的值是在参数属性success函数的参数中获取的，假如我们在第一次ajax请求后，要进行第二次ajax请求并且这一次请求的参数是第一次success获的值，如果还有第三次呢，那就必须这样写：

```js
$.ajax({
    url: '',
    data: {...},
    success: function(res){
        $.ajax({
            data: {res.data},
            success: function(res){
                $.ajax(...)
            }
        })
    }
})
```

这样就形成了常说的“回调地狱”，不过在ES6中，Promise语法可以解决这样的问题。·Promise可以认为是一种任务分发器，将任务分配到Promise队列，执行代码，然后等待代码执行完毕并处理执行结果。简单的用法如下：

```js
var post = function(url, data) {
    return new Promise(function(resolve, reject) {
        $.ajax({
            url: url,
            data: data,
            type: 'POST',
            success: function(res){
                resolve(res);
            },
            error: function(err) {
                reject(err);
            }
        });
    });
}
post('http://127.0.0.1:8080/order', {id:1}).then(function(res){
    // 这里返回成功的内容
}, function(err){
    // 这里是报错信息
});
```

上面的代码封装了jquery的ajax请求，将POST的请求进行了封装，post(..)函数内部返回了一个Promise对象，Promise对象有一个then方法，then方法的第一个参数是resolve回调函数表示成功的操作，第二个参数是reject回调函数表示失败或异常的操作。其实，Promise还有一个catch方法也可以获取reject回调函数，如post也可以这样使用：

```js
post('http://127.0.0.1:8080/order', {id:1}).then(function(res){
    // 这里返回成功的内容
}).catch(function(err){
    // 这里是报错信息
});
```

### 事件循环机制

放到该篇文章阐述：[JavaScript事件循环及异步原理详解](/front-end/javascript/事件循环及异步原理)

### 对象与class

参考[阮一峰class介绍](http://es6.ruanyifeng.com/#docs/class)

### 模块化

后面再单独分享
