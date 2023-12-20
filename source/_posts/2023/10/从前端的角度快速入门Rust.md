---
title: 从前端的角度快速入门 Rust
date: 2023-10-17 11:44:43
tags:
  - rust
---

# 前言

Rust 是一门系统编程语言，与 JavaScript 不一样的是，它是一门编译型语言，类比 C++。

目前有很多前端工具是基于 Rust 写的，暴露的 API 则通过 [napi-rs](https://napi.rs/cn) 转换成 nodejs 模块，例如 [SWC](https://swc.rs/) 。

一些新的工程化工具，例如字节的 [Rspack](https://www.rspack.dev/)；媲美 Electron 的跨平台桌面开发框架 [Tauri](https://tauri.app/)。

可以说很多对性能或内存安全的应用，都可以用 Rust 重写。对于前端开发者来说，如果想熟悉一门编译型语言，Rust 是一个很好的选择。

关于怎么安装 Rust ，请直接浏览[官网安装文档](https://www.rust-lang.org/zh-CN/learn/get-started) 

# Hello World

安装好之后，在终端内输入以下命令可以查看对应版本

```bash
# 查看 rust 版本
rustc --version
# 查看 cargo 版本
cargo --version
```

> cargo 是 Rust 官方的包管理工具，类似 nodejs 里面的 npm，yarn 这些

随便选一个目录创建一个 hello.rs 文件，打开文件写入如下代码：

```rs
fn main() {
    println!("Hello, world!");
}
```

在终端内进入该文件所在目录，输入 `rustc hello.rs` 进行编译，执行完毕后在相同目录下会出现一个 index 可执行文件（Windows下带后缀 .exe），然后在终端内执行 `./hello` （Windows下是 `./hello.exe`），终端内输出 “Hello, world!”

从上面的代码中，我们可以看出：
1、Rust 使用 `fn` 作为函数声明的关键字，如 JavaScript 里面用的是 `function`
2、Rust 的入口函数是 `main` 函数
3、`println!` 是一个终端输出函数，接收字符串，在执行时输出
4、Rust 代码需要编译后才能执行

> println 后面有个 `!` 号，这表示其是一个宏函数，后续会说明，可以先把它类比 JavaScript 的全局函数

# 通用编程概念

这一节介绍所有编程语言里面最基础通用的概念，Rust 也是，核心部分很多语言都有共同点，这些都是基础内容。

## 变量与常量

### 局部变量

Rust 中声明变量使用 `let` 关键字，例如：

```rs
let a = 1;
let s = "hello world";
```

与 JavaScript 不同的是，let 声明的变量，不能重新赋值，这跟 Rust 的所有权限制有关，后续会说明，记住这一点就行，例如下面这段代码会报错：

```rs
let a = 1;
a = 2; // 报错
```
![](https://blog-1257256187.cos.ap-chengdu.myqcloud.com/blog-imgs/202310171511621.png?imageMogr2/format/webp)

从报错信息的提示文案中 “help: consider making this binding mutable: `mut a`”，可以看出，它提示我们可以加个 `mut`，这涉及到另外个关键字了。

在 Rust 里面，变量默认是不可变的，如果要可变，需要加 `mut`，例如：

```rs
let mut a = 1;
println!("a = {}", a);
a = 2;
println!("a = {}", a);
```

> Rust 中声明的变量必须得用到，例如上面代码，如果去掉第二行，虽然不会报语法错误，但是会报警告，因为在将 a 赋值为 2 之前，a 并没有被用到，所以 Rust 编译器会给出警告： “warning: value assigned to `a` is never read”。

### 全局变量

全局变量的生命周期是整个程序，从启动到退出，它占用的内存空间是固定的，不会在执行过程中被回收。全局变量的声明使用 `static`，使用全局变量声明时，必须显式标明变量类型，例如：

```rs
static A: i32 = 1;
static mut N: i32 = 5;
```

可以看出，Rust 的变量标明跟 TypeScript 类似，都是使用冒号 `:` 隔开，后面跟类型。

### 常量

常量的生命周期也是整个程序，用 `const` 关键字声明，与 `static` （不可变变量）不同的是，编译器并不一定会为其分配内存空间，在编译过程中，可能会被内联优化，类似 C 语言的宏定义：

```rs
const N: i32 = 5;
```

> 不论是 `static` 声明的变量还是 `const` 声明的常量，都建议使用大写蛇形命名，形如 `SCREAMING_SNAKE_CASE`，这是 Rust 的规范建议。局部变量、函数和方法，都建议使用小写蛇形命名，形如 `snake_case`。如图：
> ![](https://blog-1257256187.cos.ap-chengdu.myqcloud.com/blog-imgs/202310171546009.png?imageMogr2/format/webp)

## 注释

```rs
/// 外部注释
mod test {
    //! 包/模块级别的注释
    
    /** 外部块文档注释 */
    pub fn foo() {
        /*! 内部块文档注释 */

        // 行注释
        /* 块注释 */
    }
}
```

注释内容支持 markdown 语法，使用 `cargo doc` 可以生成 HTML 文档

> 这里的 `mod` 是包管理和模块的关键字，后续章节说明

## 基础数据类型

### 数值类型

整数类型：

|长度	|有符号类型	|无符号类型|
|---|---|---|
|8 位	|i8	|u8 |
|16 位	|i16	|u16|
|32 位	|i32	|u32|
|64 位	|i64	|u64|
|128 位	|i128	|u128|
|视架构而定	|isize	|usize|

> isize 与 usize 取决于当前执行程序的 CPU 类型，如果是32位的，则等同于i32和ui32，类似的如果是64位，则等同于i64和ui64。像`let i = 1;` 这种类型推导，Rust整型会默认使用 `i32` 类型。

浮点数类型： `f32` 和 `f64` ，Rust 的浮点数类型跟 JavaScript 一样，在处理 0.2 + 0.1 的时候并不等于 0.3，因为浮点数在内存中是以二进制存储的

> 整型溢出，在 Debug 模式下 Rust 会崩溃（panic）。但是 Release 模式下则默认采取补码循环溢出处理，例如在 `u8` 类型下，256 变成 0，257 变成 1，以此类推。
> 要显式处理溢出，可以通过标准库提供的针对原始数值类型的下列方法处理：
> - `wrapping_*` 所有模式下，都按照补码循环溢出处理
> - `checked_*` 发生溢出时，返回 `None` 值
> - `overflowing_*` 发生溢出时，返回 `(n, is_over)` 元组，其中 `n` 为补码循环溢出后的值，`is_over` 是布尔值，表示是否溢出
> - `saturating_*` 发生溢出时，返回当前类型的最大值或最小值
> 上述的 * 指代 `add`、`sub`、`mul`、`div`等命名

### 布尔类型

`bool` 类型，只有两个值 `true` 和 `false`，布尔值占用内存大小为 1 个字节。

### 字符类型

`char` 字符类型使用单引号，与 C 语言不同的是，Rust中所有的 `Unicode` 值都可以作为字符，包括单个中文、日文、Emoji表情符号等，都是合法字符，例如：

```rs
fn main() {
    let c = 'z';
    let z = 'ℤ';
    let g = '国';
    let heart_eyed_cat = '😻';
}
```

> 一个 Unicode 字符占用 4 个字节，因此字符类型的内存占用为 4 个字节
> ```rs
> fn main() {
>    let x: char = 'z';
>    println!("字符'z'占用了{}字节的内存大小",std::mem::size_of_val(&x));
> }
> ```
> 

### 单元类型

`()` 类型，表示空元组，即没有任何元素的元组

> 例如上面字符类型章节的代码里面，`main` 方法没有返回值，其实就是默认返回值为 `()` ，这种也叫做 `发散函数( diverge function)`，另外也可以用 `()` 作为 map 的值，表示不关注具体的值，只关注 `key`。
> 在一些程序中，会经常看到处理 `Result` 时用 `Ok(())` 作为值，表示处理成功，不关心返回值，后续错误处理章节说明。

### 字符串

`str` 字符串字面量类型使用双引号，**与字符不同的是，字符串使用的是 UTF-8 编码，也就是字符串中的字符所占字节数是变化的（1~4）**，`str` 类型是硬编码进可执行文件，无法被修改，在使用变量指向时，通常是指向其

```rs
fn main() {
    let s: &str = "Hello😻";
    println!("字符串\"{}\"，其类型大小为 {} 字节， 值大小为 {} 字节", s, std::mem::size_of_val(&s), std::mem::size_of_val(s)); // 字符串"Hello😻"，其类型大小为 16 字节， 值大小为 9 字节
}
```

从上述代码中可以看出，s 并不是 `str`，而是 `&str` 类型，也就是字符串切片。这是因为，str 字符串字面量是直接硬编码写入的，所以绑定变量时，是直接将 `str` 的地址返回给变量 s，如果有两个相同的字符串字面量，绑定不同的变量名，其地址是相同的。

下文的集合章节里，会进行详细说明。

## 运算符

基本上，JavaScript 里面有的运算符，Rust 都支持。

**数值运算：** `+`、`-`、`*`、`/`、`%`，以及 `+=`、`-=`、`*=`、`/=`、`%=`，以及按位计算符号：`&`、`&=`、`|`、`|=`、`^`、`^=`、`>>`、`>>=`、 `<<`、`<<=`

**逻辑运算：** `&&`、`||`、`!`，`<`、`<=`、`>`、`>=`、`!=`

**其他运算符**：

| 符号 | 解释 | 例子|
|---|---|---|
| ! | 宏展开 | `println!()` |
| & | 借用 |  |
| * | 解引用 |  |
| * | 裸指针 | |
| -> | 函数与闭包，返回类型 | |
| . | 成员访问 |  |
| .. | 右半开区间 |  |
| ..= | 右半闭区间 |  |
| : | 类型约束 | `let i: u8 = 10;` |
| ; | 语句结束符 | `println!();` |
| ; | 固定大小数组个数分隔符 | `let a: [i32; 5] = [1, 2, 3, 4, 5];` |
| ? | 错误传播 | |


> Rust 没有 `++` 和 `--` 这两个运算符。
> 字符串不支持 `+` 拼接，可以使用 `format!` 宏，后面会说明；

## 流程控制

### 表达式与语句

Rust 中的语句就是指通过 `;` 结尾的代码，而表达式，可以简单理解成某个运算，其有值返回，例如 `5 + 6` 就是个表达式，如下：

```rs
fn main() {
    let y = {
        let x = 3;
        x + 1
    };

    println!("The value of y is: {}", y);
}
```

代码中，`3` 和 `x + 1` 是个表达式；`{ let x = 3; x + 1 }` 也属于语句块表达式，其运算后值是 `x + 1` 也就是 4，赋值给变量 `y`。

> 注意，`x + 1` 不能以 `;` 结尾，否则其就是个语句，语句不会返回值，程序就会报错。所以一般的，在一些需要有返回值的函数内，可以直接在函数末尾使用表达式，而不需要写 `return` 关键字返回值。

### if - else 分支控制

Rust 的 if 与 JavaScript 不同在于，它不需要用括号括起来，例如：

```rs
if condition == true {
    // A...
} else {
    // B...
}
```

当然，也支持 if - else if 这种处理多重条件，例如：

```rs
if a == 1 {
    // A...
} else if a == 2  {
    // B...
} else {
    // C...
}
```

if 语句块是表达式，支持返回值赋值，例如：

```rs
fn main() {
    let condition = true;
    let number = if condition {
        5
    } else {
        6
    };

    println!("The value of number is: {}", number); // 5
}
```

> 需注意的是，if 语句块返回值赋值的话，需要保证每个分支下的返回类型是一致的，如果不一致，会报错！


### 循环

**for in 循环语句**

for 循环是最常用的，例如：

```rs
fn main() {
    for i in 1..=5 {
        println!("{}", i); // 1, 2, 3, 4, 5
    }
}
```

跟 JavaScript 的 `for - of` 很像，`..=` 表示的是一个闭合区间，其可以理解成 `[1,2,3,4,5]` 的 `i32` 数组。

> 需要注意的是，for 循环时，是将集合的所有权进行了转移（`move`），所以一般情况下，集合需要通过引用（`&`）来获取，后续引用章节详细说明，先记住有这个注意点。而基本的数据类型`[i32; n]` 这种，已经实现了 `copy` 特征，所以不需要转移所有权。

那如果有一个数组，怎么获取其索引呢？如代码：

```rs
fn main() {
    let a = [4, 3, 2, 1];
    // `.iter()` 方法把 `a` 数组变成一个迭代器
    for (i, v) in a.iter().enumerate() {
        println!("第{}个元素是{}", i + 1, v);
    }
}
```

那就是遍历其迭代器，迭代器每次遍历返回的是一个元组，第一个值是索引，第二个值是值。

**while 循环语句**

跟 JavaScript 一模一样，例如：

```rs
fn main() {
    let mut n = 0;
    while n <= 5  {
        println!("{}!", n);
        n = n + 1;
    }
    println!(n);
}
```

**loop 循环表达式**

Rust 特殊的循环关键字，后面跟一个语句块，组成 loop 表达式，块内死循环，除非通过 `break` 跳出循环，例如：

```rs
fn main() {
    let mut counter = 0;
    let result = loop {
        counter += 1;
        if counter == 10 {
            break counter * 2;
        }
    };
    println!("The result is {}", result);
}
```

在 Rust 中，`continue` 和 `break` 也是支持的，并且在 loop 表达式中，break 可以携带返回值

> 一般的循环，在 `while` 和 `for` 中建议使用 for 的方式，因为 for 通过迭代器选择，会避免运行时的边界索引检查，性能更好。而 `loop` 表达式，更适用于简单的循环需要返回值的情况。

## 函数

来见一个最常用的 `add` 函数，例如：

```rs
fn main() {
    let sum = add(3, 2);
    println!("sum is {}", sum);
}

fn add(i: i32, j: i32) -> i32 {
    i + j
}
```

可见，函数声明是用 `fn` 关键字，参数必须携带类型，返回值可选，如果需要返回值，则需要使用 `->` 指定返回值类型。

> 函数没有显示使用 `->` 指明返回值类型，则表示其返回的值是 `()` ，即单元类型，也可以理解没有返回值。

在 Rust 中，不支持可选参数，但是可以通过 `Option` 枚举来实现类似效果，后续枚举章节说明。

# Cargo包管理工具

Cargo 是官方的包管理工具，安装好 rust 之后会默认安装好 `cargo`，类似 node 下的 `npm`。

**创建项目**

```bash
cargo new demo
```

创建一个名为 `demo` 的项目目录，其中 `demo` 就是项目的名称，连 git 也会一同创建。



其结构如下：

```bash
learn/
├── Cargo.toml
├── Cargo.lock
└── src
    └── main.rs
```

main.rs 是入口文件，其内的 `fn main` 方法是入口函数。Cargo.toml 文件是项目数据描述文件，类似 node 项目里面的 `package.json`。

Cargo.lock 文件是项目依赖清单文件，不要手动修改，在依赖库项目情况下，建议添加到 `.gitignore` 里面。

**运行项目**

进入到项目目录下，执行如下命令，会运行项目

```bash
cargo run
```

**编译项目**

```bash
cargo build
```

`cargo run` 和 `cargo build` 默认是采用 Debug 模式，这种模式编译速度会很快，但执行性能较慢， 可以在命令后面加上 `--release`，保证执行性能，但编译速度会变慢。

**检查代码**

```bash
cargo check
```

## 定义项目依赖

在 `Cargo.toml` 文件中，可以定义项目依赖，例如：

```toml
[dependencies]
rand = "0.3"
hammer = { version = "0.5.0"}
color = { git = "https://github.com/bjz/color-rs" }
geometry = { path = "crates/geometry" }
```

上面列出了三种依赖方式，分别是
- 基于官方仓库 crates.io，通过版本说明来描述
- 基于项目源代码 git 仓库地址，通过 url 描述
- 基于本地项目绝对路径或相对路径，通过类 Unix 模式路径描述

后续涉及到的代码，均表示在 cargo 构建的可运行项目里面编写，后续内容，除写出 `fn main` 外，所有代码表示在 main.rs 内的 main 方法内运行。

# Rust的特殊概念

Rust 的一大特点，就是其内存安全性，与其他通过 GC（垃圾回收） 方式实现内存安全的语言（Java、Go、JavaScript等）不同的是，Rust 采用了与众不同的方式：**所有权系统**，从而规避了 GC 可能带来的性能、内存占用等问题。

## 所有权与借用

所有程序运行，都需要内存，所以程序执行过程中是不断申请内存和释放内存的过程。所以在不需要的时候释放内存，是编程语言的重要工作之一。一般有下面三种选择：

- **垃圾回收机制**，在程序运行时不断寻找不再使用的内容进行释放，代表：Java、JavaScript、Go、C#、Python等
- **手动管理内存分配与释放**，在程序代码中，需要开发者手动申请和释放内存，代表：C、C++
- **通过所有权管理内存**，编译器在编译时根据一系列规则检查，代表：Rust

所有权检查发生在编译阶段，所以对运行时不影响，故而不影响程序性能。可以理解成与 C++ 不同的是，就是 Rust 编译器在编译时自动根据它提供的所有权机制，帮你的代码实现了管理内存分配和释放的部分。

### 栈和堆

**栈**

栈是一种最基础的数据结构，符合**后进先出**规则，与 JavaScript 一样，Rust 的基本数据类型也是存储在栈内的。栈存储的数据必须满足其大小是可知的。

**堆**

堆是一种树形结构，与栈不同的是，堆存储的数据大小是未知的或变化的。当往堆上放入数据时，需要申请一定大小的空间放入数据，将空间标记为已用并返回一个表示该位置地址的**指针**，指针绑定给变量，存储在栈上。这一点，与 JavaScript 的引用类型也很相似。

> 性能对比：
> 写入方面：入栈比在堆上分配内存要快，引入入栈时只需要移动栈顶指针放入数据即可，堆上分配内存需要操作系统去寻找足够存入数据的内存空间，接着记录为下一次做准备。
> 读取方面：栈数据可以直接存储在 CPU 高速缓存中，而堆数据只能存放内存中，访问栈数据比访问堆数据快。


### 所有权

Rust 的所有权里面，有两个概念，分别是 **移动（move）** 与 **拷贝（copy）**。所有权遵循下面原则：

1、**Rust 中每一个值都被一个变量所拥有，该变量称为值的所有者**
2、**一个值同时只能被一个变量所拥有**
3、**当所有者（变量）离开作用域范围时，这个值将被丢弃（drop）**

上文中说到，基础数据类型中字符串字面量是写入编码里面的，长度不可变，如果需要使用可变的字符串呢？Rust 标准库提供了 `String` 类型，我们后续借助 `String` 类来理解所有权相关的知识。


#### move

```rust
let s1 = "hello";
let s2 = s1;
pintln!(" s1: {}, s2: {}", s1, s2);
```

上述这段代码执行不会有任何的问题，但是看下面这段代码：

```rust
let s1 = String::from("hello");
let s2 = s1; // 此时，s1 变量已经失效，无法访问
pintln!("s1: {}, s2: {}", s1, s2); // 因为上一步已经将 s1 move 给 s2，所以这里无法在继续访问 s1 变量
```

这段代码在编译时会报错，因为在 Rust 中，**当一个变量被赋值给另一个变量时，其所有权会转移**，也就是**move**，当所有权被 move 之后，再继续访问时已经访问不到。如图：

![](https://blog-1257256187.cos.ap-chengdu.myqcloud.com/blog-imgs/202312191816880.png?imageMogr2/format/webp)

图中，当 s1 被赋值给 s2 时，Rust 不允许存在多个变量包含相同内存地址，会删去 s1 ，将堆中的内存地址给到 s2，所以后续继续使用 s1 时会报错。

但是在第一段代码，却没有报错，是因为**只在栈上的数据，是直接拷贝值**。其实是 Rust 有一个叫做 `Copy` trait 的特殊标注（特征），可以用在类似字符串字面量这样的存储在栈上的类型，所以如果一个类型实现了 `Copy` trait，那么一个旧的变量在将其赋值给其他变量后仍然可用。详细可参考 [所有权](https://rustwiki.org/zh-CN/book/ch04-01-what-is-ownership.html#%E4%BB%80%E4%B9%88%E6%98%AF%E6%89%80%E6%9C%89%E6%9D%83)

> 注意：所有权原则是针对放在堆中的数据，栈中的数据不受所有权原则约束。因为函数执行本来就是一个入栈出栈的过程，栈中的数据在函数执行完后，会自动释放。

#### copy

Rust 中除了通过 `Copy` trait 可以进行拷贝值的操作外，有些类型提供了 `clone` 方法，例如上述代码可以改成：

```rust
let s1 = String::from("hello");
let s2 = s1.clone();
pintln!("s1: {}, s2: {}", s1, s2);
```

此时，其在内存中的分配如图：

![](https://blog-1257256187.cos.ap-chengdu.myqcloud.com/blog-imgs/202312191816565.png?imageMogr2/format/webp)

这种情况，就是将 s1 在堆中的数据直接进行了一次新的拷贝，s2 指向其拷贝值的内存地址。

> 总结：Rust 的非栈内数据或 `Copy` trait 下的变量声明与赋值，可以看作是所有权的声明与 move，其遵守了所有权原则。

### 引用与借用

很多语言里面，都有内存地址的引用，比如 js 里面的引用类型，其变量存储的是数据的内存地址。在 Rust 中，所有权像是引用上加了一层职责约束。为了区分，Rust 在引用的基础上出现了**借用**这个概念，在阐述这个之前，来看看 Rust 函数的参数及返回值。

#### 函数上的 move

函数的参数赋值，与变量赋值是一样的，也是 move 操作，即所有权转移。直接看代码：

```rust
fn main() {
    let a = String::from("kevin");
    say_hello(a); // a 作为函数参数，所有权被 move 给函数内作用域的 name 变量
    print!("{}", a); // 此时会报错，这个时候已经拿不到 a 了，因为 a 变量的数据所有权变量已经被 move 了
}

fn say_hello(name: String) {
    println!("Hello, {}!", name);
}
```

举一反三，那函数的返回值，也同样会 move 操作，即所有权转移：

```rust
fn main() {
    let a = get_name(); // get_name 作用域内的 name 变量 move 给 a 变量
    say_hello(a);
}

fn get_name() -> String {
    let name = String::from("kevin");
    name // 返回 String
}

fn say_hello(name: String) {
    println!("Hello, {}!", name);
}
```

> 需要注意的是，上面所说的情况，都适用于未实现 `Copy` trait 的类型，例如整数，字符串字面量这些实现了 `Copy` trait 的类型，其是直接拷贝的，而不是转让所有权。

#### 借用

因为 Rust 的所有权大部分情况是 move 的原因，在代码中需要大量调用函数，传参与返回值，那么就需要大量的所有权转移，代码看起来会比较冗长，而且容易出错，所以 Rust 引入了**借用**的概念。

代码如下：

```rust
fn main() {
    let a = String::from("kevin");
    say_hello(&a);
    print!("{}", a);
}

fn say_hello(name: &String) {
    println!("Hello, {}!", name);
}
```

与之前的代码对比，`say_hello` 的参数 `name` 成了 `&String` 类型。**借用**就是指创建引用的行为。Rust 中使用 `&` 来创建对某一个类型的借用，**借用没有所有权，只有使用权**。如图：

![](https://blog-1257256187.cos.ap-chengdu.myqcloud.com/blog-imgs/202312191902394.png?imageMogr2/format/webp)

需要注意的是，借用与 C++ 语言你们的指针不同，不能修改被借用的数据，例如：

```rust
fn main() {
    let a = String::from("hello");
    change(&a);
    println!("{}", a);
}

fn change(str: &String) {
    str.push_str(", world"); // 报错，error: cannot borrow `*str` as mutable, as it is behind a `&` reference
}
```

上述代码中，在 change 函数里面去修改内容，产生了报错。那如果，就是想通过这种方式，在函数里面去修改传入参数的内容呢，可以通过**可变引用**实现：

```rust
fn main() {
    let mut a = String::from("hello"); // 变量声明成可变变量
    change(&mut a);
    println!("{}", a); // hello, world
}

fn change(str: &mut String) { // 传入的参数是可变引用
    str.push_str(", world");
}
```

首先，需要将变量 `a` 声明成 `mut`，然后调用的函数参数也必须是可变引用 `&mut a`，这样就很清晰的表明，`change` 函数会改变它所借用的值。

但是可变引用有个限制：**同一作用域下，只能有一个对某一特定数据的可变引用**，如：

```rust
let mut s = String::from("hello");
let r1 = &mut s;
let r2 = &mut s; // 已经有可变引用了，第二次可变引用会报错
println!("{}, {}", r1, r2);
```

还有种情况，**同一个作用于，已经有对某个特定数据的引用下，不能对该数据使用可变引用**，如：

```rust
let mut s = String::from("hello");
let r1 = &s; // 没问题
let r2 = &s; // 没问题
let r3 = &mut s; // 报错
println!("{}, {}, and {}", r1, r2, r3);
```

注意，**一个引用的作用域从声明的地方到最后一次使用为止**，基于此，上面代码可以改成：

```rust
let mut s = String::from("hello");
let r1 = &s; // 没问题
let r2 = &s; // 没问题
println!("{} and {}", r1, r2);
// 此位置之后 r1 和 r2 不再使用
let r3 = &mut s; // 没问题
println!("{}", r3);
```

所以，如果出现可变引用的问题，需要注意变量的作用域或引用的作用域问题。

## 生命周期

## 智能指针

## 特征

# 集合

## 字符串与切片

字符串拼接

## 结构体与枚举

## 元组

## 数组


# 接口与泛型


# 错误处理


# 包管理与模块化


# 自动化测试


# 进阶内容

## 面向对象

## 并发

## 高级特征

## 高级函数与闭包

