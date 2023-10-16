---
title: 三小时入门PHP5
date: 2020-04-05 16:07:10
tags:
---

# 三小时入门PHP5基本语法

## PHP是什么？
PHP是一门后端动态解释型计算机高级语言，一般用来编写或者生成动态网页，主要**负责数据的处理与渲染**。（这里是指用PHP嵌入网页里面的形式，现在可以直接用一些JS的框架去渲染网页数据了，PHP主要用来做数据处理）

>在学习PHP之前，你需要了解HTML，最好有C语言或者C++，Python等其他高级语言基础

PHP一般**需要与web服务器软件一起使用**，如**Apache，Nginx，IIS**这些服务器软件，当然这些软件首先需要配置好PHP环境。推荐使用**phpstudy**这个软件在你的电脑上创建自己的PHP学习环境，[点击下载phpstudy](http://www.phpstudy.net/phpstudy/phpStudy20161103.zip)，如果链接失效请到http://www.phpstudy.net/自行搜索下载

### 怎么使用phpstudy
> 推荐一个更加方便的集成环境工具，laragon.exe可以很方便的部署php开发环境并且能够自动完成虚拟主机的配置[下载链接](http://t.cn/EVWsHss)


1.下载完成后解压到D:\phpstudy\目录下

2.打开phpstudy这个软件，会自动开启Apache的web服务和MySQL数据库服务，如果都为绿色的点表示服务开启成功
![image.png](https://user-gold-cdn.xitu.io/2019/2/19/169046f9611c337c?w=491&h=408&f=png&s=65051)

3.在浏览器地址栏输入localhost按下回车，出现下面这个网页表示安装成功
![image.png](https://user-gold-cdn.xitu.io/2019/2/19/169046f9612414ae?w=1240&h=607&f=png&s=234762)


4.在D:\phpstudy\www\目录里面放入你写的PHP文件，如我放入一个叫做test.php的文件，该文件内容如下面HelloWorld标题代码所示

5.在浏览器地址栏输入localhost/test.php，按下回车键即可打开该网页，输出了Hello World
![image.png](https://user-gold-cdn.xitu.io/2019/2/19/169046f961511533?w=402&h=187&f=png&s=13486)

## Hello World
像下面这样直接嵌入网页中输出HelloWorld，直接用echo指令输出到网页上

```php
<!DOCTYPE html>
<html lang="zh-cn">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <meta http-equiv="X-UA-Compatible" content="ie=edge">
  <title>Document</title>
</head>
<body>
  <?php
  echo "Hello World";
  ?>
</body>
</html>
```

>这个文件必须保存为.php文件，不能保存为.html文件，否则里面的<?php ... ?> 这一段不会被解析

可以看出，在php文件里面含有html代码时PHP的代码片段以 “`<?php` ” 开始以 “  `?>` ”结束，如果php文件里面不含任何HTML的代码，则只需要写 `<?php`


## 注释
跟大多数编程语言一样

 // 用在单行注释
 
/* 和 */ 用在多行注释


## 变量
用$后面跟上变量名初始化和使用变量，如：
```php
<?php
$x=5;
$y=6;
$s="Hello World!";
$z=$x+$y;
echo $z.$s;   //输出11Hello World
?>
```
可以看出PHP是一种弱类型的语言，用$声明的变量既可以是数字也可以是字符串等其他的数据类型，其中字符串的连接符是用" __.__ "，可以将多个字符连接起来，如
```php
<?php
$x = "Hello";
$y = 'World';
$z = $x.$y;
echo $z;   //输出HelloWorld
```
细心的朋友会注意到$y = 'World'; 这一句用的是单引号，其实PHP里面单引号和双引号都可以表示字符串，但是必须要成对出现。
>PHP的语句和变量是严格区分大小写的，神奇的是，PHP还有很多特殊的全局变量，对于程序的编写有很大的便利，将在后面说明


## 常量
PHP中的常量使用 __define()__ 函数，函数语法如下：
```php
bool define ( string $name , mixed $value [, bool $case_insensitive = false ] )
```
其中，

name : 常量名（不需要加$符号）

value : 常量的值（可以为任意数据类型）

case_insensitive： 默认为false区分大小写，若为true则不区分常量名的大小写
如：
```php
<?php
define("PI",3.14);
$r = 2;
echo PI*$r*$r;    //输出 12.56
```


## 数据类型
PHP中有如下几种数据类型：
String（字符串）, Integer（整型）, Float（浮点型）, Boolean（布尔型）, Array（数组）, Object（对象）, NULL（空值）
前三种前面已经使用过了，简单介绍下字符串和后面几种，关于对象后面再详细说明：
### 字符串
常用字符串的处理函数

**strlen()** ： 获取字符串长度
```php
$s = "Hello World!";
echo strlen($s);   //输出12
```
特别的，对于中文来说
```php
<?php
echo strlen("你好世界");   // 输出 12
```
因为在默认编码下一个中文占3个字符数，可以使用mb_strlen()指定编码输出
```php
<?php
echo mb_strlen("你好世界","utf-8");   // 输出 4
```
**strpos()** : 在字符串内查找一个字符或一段指定的文本。
如果在字符串中找到匹配，该函数会返回第一个匹配的字符位置。如果未找到匹配，则返回 false。 
```php
<?php 
echo strpos("Hello world!","world");  // 输出6
```
**md5()** ： 计算字符串的 MD5 散列，通常用于简单加密
```php
<?php
echo md5("password");   //输出5f4dcc3b5aa765d61d8327deb882cf99
```

### 布尔型
true 和 false 两个值
例如：
```php
<?php
$x = true;
var_dump($x);    //输出bool(true) 
```
>var_dump() 函数返回变量的数据类型和值

### 数组
PHP中用函数 **array()** 来创建数组，其中有索引数组，关联数组，多维数据
**索引数组**（用从0开始的下标表示数组里面对应的值）
```php
<?php
$student = array("Kevin","Bob","Tom");
echo $student[0]." Love ".$student[2];    //输出Kevin Love Tom
```
> 可以使用函数count($student) 获取数组$student的长度为3

**关联数组** （含有键值对" key => value "的数组，每个value值都有它的key值）
```php
<?php
$age = array("Kevin"=>18,"Bob"=>26,"Tom"=>17);
echo $age['Kevin'];    //输出18
```
**多维数组**
```php
<?php
$sites = array 
( 
    "runoob"=>array 
    ( 
        "百度", 
        "http://www.baidu.com" 
    ), 
    "google"=>array 
    ( 
        "Google 搜索", 
        "http://www.google.com" 
    ), 
    "taobao"=>array 
    ( 
        "淘宝", 
        "http://www.taobao.com" 
    ) 
); 
echo $sites['taobao'][1];    //输出http://www.taobao.com
```
对于数组的排序，可以看下这个教程 http://www.runoob.com/php/php-arrays-sort.html
### 空值
NULL 值表示变量没有值。可以通过将变量赋值为空值来清空变量数据。
## 运算符
PHP里面的很多运算符其实跟C语言一样的，所以没有很多要讲的，其中注意一下下面这些运算符：

|运算符|作用说明|
|:---|:---|
|  **.**  |点号，并置运算符，用于连接字符串|
| === |绝对等于，用于判断，表示两个变量类型和值都相等|
| + | 即表示数学上算术相加，也可以用在两个数组连接|

对运算符不是很了解的可以看看这个教程：http://www.runoob.com/php/php-operators.html

## 逻辑语句
### 判断语句
if 语句 - 在条件成立时执行代码
if...else 语句 - 在条件成立时执行一块代码，条件不成立时执行另一块代码
if...elseif....else 语句 - 在若干条件之一成立时执行一个代码块
switch 语句 - 在若干条件之一成立时执行一个代码块
特别地，要记得在switch语句里面用break;跳出每一个选项。
### 循环语句
像C语言一样的循环
while - 只要指定的条件成立，则循环执行代码块
do...while - 首先执行一次代码块，然后在指定的条件成立时重复这个循环
for - 循环执行代码块指定的次数
着重说一下PHP特有的foreach循环：
foreach主要是用来遍历数组的，如下所示：
```php
<?php
$age = array("Kevin"=>18,"Bob"=>26,"Tom"=>17);
foreach ($age as $name=>$a )
{
  echo $name."的年龄为".$a."<br>";
}
/*输出
Kevin的年龄为18
Bob的年龄为26
Tom的年龄为17
*/
```

## 函数
PHP像这样声明一个函数
```php
<?php
function 函数名(参数列表)
{
    // 要执行的代码
}
```
> 参数可以有默认值，函数的返回值用return直接返回，可以返回任意数据类型的返回值

函数的变量作用域：
如在下面的程序中

```php
<?php 
$x=5; // 全局变量 
function myTest() 
{ 
    $y=10; // 局部变量 
    echo "变量 x 为: $x";   //会报错Notice: Undefined variable: x in D:\phpstudy\WWW\test.php on line 6
    echo "<br>";          //换行
    echo "变量 y 为: $y";   //输出变量y为：5
}  
myTest(); 
```
为什么第6行代码会报错呢?因为在函数内部里面是不能直接访问全局变量$x的，函数内部初始化的变量$y也只能在函数内部起作用，如果需要在函数内使用全局变量，则需要先在函数内声明，使用global指令

```php
<?php 
$x=5; // 全局变量 
function myTest() 
{ 
    global $x;     //声明全局变量$x
    $y=10; // 局部变量 
    echo "变量 x 为: $x";   //输出变量x为：5
    echo "<br>";    //换行
    echo "变量 y 为: $y";   //输出变量y为：5
}  
myTest(); 
```

## PHP的特殊变量
### 魔术变量
`__FILE__`  : 文件的完整路径和文件名。
`__LINE__` : 文本中的当前行号。
`__DIR__` ：文件所在当前目录。
`__FUNCTION__`：用在函数里面，返回函数的名字。
`__CLASS__` ：用在类里面，返回类的名字。
`__NAMESPACE__`：返回当前命名空间的名字。
### 超级全局变量
该内容详见教程：http://www.runoob.com/php/php-superglobals.html

`$GLOBALS`: 是一个关联数组，任何声明的全局变量都会保存到这个数组里面去，该数组的key值是全局变量的名字，value值是全局变量的值

`$_SERVER`：是一个包含了诸如头信息(header)、路径(path)、以及脚本位置(script locations)等等信息的数组。这个数组中的项目由Web服务器创建。不能保证每个服务器都提供全部项目；服务器可能会忽略一些，或者提供一些没有在这里列举出来的项目。

`$_REQUEST`：用于收集HTML表单提交的数据。

`$_POST`：收集表单通过POST方式上传的数据(在HTML里面指定form的method= "POST")

`$_GET`：同样被广泛应用于收集表单数据，也用于URL发送的数据

## 命名空间
命名空间是为了避免与PHP内部的类/变量/函数或者第三方类库名字冲突引入的相当于别名的东西，通过使用不同的命名空间和设置别名可以避免这些冲突。

声明命名空间：
`namespace 名称;`
使用命名空间：
`use 名称 as 别名;`
其中“as 别名”也可以省略。

一般放到PHP代码文件的首行。
也可以在一个文件里面使用多个命名空间，用  { }  将不同的命名空间分开
如：
```php
<?php
namespace MyProject {

const CONNECT_OK = 1;
class Connection { /* ... */ }
function connect() { /* ... */  }
}

namespace { // 全局代码
session_start();
$a = MyProject\connect();
echo MyProject\Connection::start();
}
```
## 面向对象
学过C++或者python，Java的话都知道什么是面向对象编程。

**声明类**
```php
<?php
class 类名{
    public $变量名;    //php中的变量的需要加上修饰符
    public function 函数名(){
          //函数的内容
    }
}
```
每个类有一个`$this`变量，用来代表对象本身，可以通过`$this`对象调用类的变量或方法。
创建对象使用new关键字，如下所示：
```php
<?php
class Student
{
    function __construct( $name, $age ) {      //构造函数
       $this->name = $name;
       $this->age = $age;
    }
}
// 实例化对象
$xiaoming = new Student("xiaoming",18);
echo $xiaoming->age;        //输出18
```

**继承**

使用关键字extends代表继承，如子类继承父类的形式如下：
```php
<?php
class Child extends Parent {
   // 代码部分
}
```
**访问控制**

public（公有）：公有的类成员可以在任何地方被访问。

protected（受保护）：受保护的类成员则可以被其自身以及其子类和父类访问。

private（私有）：私有的类成员则只能被其定义所在的类访问。

**抽象类**

任何一个类，如果它里面至少有一个方法是被声明为抽象的，那么这个类就必须被声明为抽象的。
定义为抽象的类不能被实例化。使用关键字abstract声明类，如下
```php
<?php
abstract class ClassName
{
    //抽象类的内容
}
```
**Static 关键字**

声明类属性或方法为 static(静态)，就可以不实例化类而直接访问。
静态属性不能通过一个类已实例化的对象来访问（但静态方法可以）。
由于静态方法不需要通过对象即可调用，所以伪变量 `$this` 在静态方法中不可用。
静态属性不可以由对象通过 -> 操作符来访问。

关于PHP的面向对象编程还有很多需要注意的地方，但是可能简单的网页后端编程里面需要掌握的语法基础就是这些了，可以多看几遍“菜鸟教程”的PHP教程：http://www.runoob.com/php/php-tutorial.html

OK到这里基本上你已经了解了PHP的语法基础，然后你需要学习的就是PHP的一些标准库的运用了，比如文件操作，GD图形库(制作图片验证码)，用PHP进行MySQL数据库操作等。

为方便网站服务器后端开发，你可以尝试去了解一下PHP的开发框架，比如国内流行的ThinkPHP5，很多特性的Laravel，以及很多人使用的Yii
