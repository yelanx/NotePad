> 本文由 [简悦 SimpRead](http://ksria.com/simpread/) 转码， 原文地址 [www.broadview.com.cn](http://www.broadview.com.cn/article/419814?hmsr=toutiao.io&utm_medium=toutiao.io&utm_source=toutiao.io)

![](http://download.broadview.com.cn/Original/21046e5603043ce913ac)

**以下文章来源于 Jacen 的技术笔记 ，作者 Jacen**

对于想要入门 C++ 的同学来说，《C++ Primer》是一本不能错过的入门书籍，它用平易近人的实例化教学激发学生的学习兴趣，帮助学生一步步走进 C++ 的大门。在本文中，作者 Jacen 用两万多字总结了《C++ Primer 中文版（第五版）》1-16 章的阅读要点，可以作为该书的阅读参考。注：原书更为详细，本文仅作学习交流使用。

### 第一章 开始

#### 1.1 编写一个简单的 C++ 程序

![](http://download.broadview.com.cn/Original/210481cf204ac13e0523)

每个 C++ 程序都包含一个或多个函数，其中一个必须命名为 main.

#### 1.2 初识输入输出

![](http://download.broadview.com.cn/Original/210495d3cf491bb4be32)

#### 1.3 注释简介

两种：  
单行注释：//  
界定符：/ _和_ /

#### 1.4 控制流

while;for;if;

### 第二章 变量和基本类型

P30-P71  
数据类型是程序的基础。C++ 语言支持广泛的数据类型。

**基本内置类型**

1. 算术类型

![](http://download.broadview.com.cn/Original/2104cde9fd256ff29e41)

2. 类型转换

不要混用符号类型和无符号类型。

**变量**

1. 变量定义

（1）基本形式：  
类型说明符，随后紧跟着一个或者多个变量名组成的列表，其中变量名以逗号分隔，最后以分号结束。  
（2）初始值  
在 C++ 中，初始化和赋值是 2 个完全不同的操作。初始化的含义是创建变量的时候赋予一个初始值，而赋值的含义是把对象的当前值擦除，用一个新值来替代。两者区别很小。  
（3）列表初始化  
用花括号来初始化变量的方式，称为列表初始化。  
（4）默认初始化  
如果定义变量没有指定初始值，则变量被默认初始化。  
::: tip  
例外情况：  
定义在函数体内部的内置类型变量将不被初始化，其值未定义。  
建议初始化每个内置类型的变量。:::

2. 变量声明和定义的关系

变量声明：规定了变量的类型和名字。  
变量定义：除声明之外，还需要申请存储空间。  
如果想声明一个变量，而非定义它，需要使用 extern 关键词。

![](http://download.broadview.com.cn/Original/2104a64cbb3a77afcb52)

::: tip 变量只能被定义一次，但可以被多次声明。:::

3. 名字的作用域

作用域：C++ 中大多数作用域都用花括号分隔。  
作用域中一旦声明了某个名字，它所嵌套的所有作用域都能访问该名字。同时，允许在内层作用域中重新定义外层作用域中有的名字。  
::: warning 如果函数有可能用到某全局变量，则不宜再定义一个同名的局部变量。:::

**复合类型**

定义: 复合类型是基于其他类型定义的类型。

1. 引用

引用：为对象起另外一个名字。  
::: warning 引用必须被初始化。引用本身不是对象，所以不能定义引用的引用。引用要和绑定的对象严格匹配。引用类型的初始值，必须是一个对象。:::

![](http://download.broadview.com.cn/Original/21044e8fa0c10f26fa0f)

**const 限定符**

定义：const 用于定义一个变量，它的值不能被改变。const 对象必须初始化。  
::: tip  
默认状态下，const 对象仅在文件内有效。当多个文件出现了同名的 const 变量时，等同于在不同文件中分别定义了独立的变量。  
如果想让 const 变量在文件间共享，则使用 extern 修饰。  
:::  
（1）const 的引用  
允许为一个常量引用绑定非常量的对象、字面值，甚至是个一般表达式。  
一般，引用的类型必须与其所引用对象的类型一致，特殊情况是表达式。  
（2）指针和 const  
弄清楚类型，可以从右边往左边阅读。  
（3）顶层 const  
top-level const 表示指针本身是个常量  
low-level const 表示指针所指的对象是一个常量。  
（4）constexpr 和常量表达式  
C++ 新标准规定，允许将变量声明为 constexpr 类型以便由编译器来验证变量的值是否是一个常量表达式。

**处理类型**

![](http://download.broadview.com.cn/Original/21046c2fbaa3646b2236)

**自定义数据结构**

（1）类  
数据结构是把一组相关的数据元素组织起来，然后使用它们的策略和方法。  
类一般不定义在函数体内，为了确保各个文件中类的定义一致，类通常被定义在头文件中，而且类所在头文件的名字应该与类的名字一样。  
头文件通常包含那些被定义一次的实体。  
（2）预处理器  
![](http://download.broadview.com.cn/Original/210454f9c0cbb6ee7c9f)  
一般把预处理变量的名字全部大写。

**术语**

**空指针 ：**值为 0 的指针，空指针合法但是不指向任何对象。nullPtr 是表示空指针的字面值常量。  
**void*：**可以指向任意非常量的指针类型，不能执行解引用操作。

### 第三章 字符串、向量和数组

P74-P118  
string 表示可变长的字符序列，vector 存放的是某种给定类型对象的可变长序列。  
**命名空间的 using 声明**  
![](http://download.broadview.com.cn/Original/2104b79b1f5d7c0e52ea)  
头文件不应包含 using 声明。

**标准库类型 string**  
![](http://download.broadview.com.cn/Original/21045f1506bdf51d8706)

**标准库类型 vector**  
![](http://download.broadview.com.cn/Original/21047b0b6ce6350e6bd6)

**迭代器介绍**  
![](http://download.broadview.com.cn/Original/21041a61ff5f886ea794)

**数组**  
![](http://download.broadview.com.cn/Original/21048cc5a1d261340098)

**多维数组**  
![](http://download.broadview.com.cn/Original/21045faf7e66f503f1bd)

**术语**  
**begin** string 和 vector 的成员，返回指向第一个元素的迭代器。也是一个标准库函数，输入一个数组，返回指向该数组首元素的指针。  
**end** string 和 vector 的成员，返回一个尾后迭代器。也是一个标准库函数，输入一个数组，返回指向该数组尾元素的下一个位置的指针。

### 第四章 表达式

P120-P151

#### 4.1 基础

重载运算符：为已经存在的运算符赋予了另外一层含义。  
左值、右值：  
当一个对象用作右值得时候，用的是对象的值（内容）。  
当对象被用作左值得时候，用的是对象的身份（在内存中的位置）。

#### 4.2 算术运算符

%：参与取余运算的运算对象必须是整数类型。

#### 4.3 逻辑和关系运算符

![](http://download.broadview.com.cn/Original/2104aac850418b5d557e)  
&& 运算符和 || 运算符都是先求左侧运算对象的值再求右侧运算对象的值。  
::: warning  
进行比较运算的时候，除非比较的对象是 bool 类型，否则不要使用布尔字面值 true,false 作为运算对象。  
:::

#### 4.4 赋值运算符

![](http://download.broadview.com.cn/Original/2104d01581fd5d407675)

#### 4.5 递增和递减运算符

递增运算符 ++  
递减运算符 —

#### 4.6 成员访问运算符

![](http://download.broadview.com.cn/Original/2104686a3bb2d774d3e1)

#### 4.7 条件运算符

![](http://download.broadview.com.cn/Original/21048a512376cc7680b9)

#### 4.8 位运算符

![](http://download.broadview.com.cn/Original/210409ba23719c53e46f)

#### 4.9 sizeof 运算符

![](http://download.broadview.com.cn/Original/2104c7e739fff161b7f8)

#### 4.10 逗号运算符

逗号运算符含有两个运算对象，按照从左向右的顺序依次求值。

#### 4.11 类型转换

![](http://download.broadview.com.cn/Original/2104f512fa9b8c09fe72)

#### 4.12 运算符优先级表

### 第五章 语句

![](http://download.broadview.com.cn/Original/2104a9ea5e55174b4c6f)  
![](http://download.broadview.com.cn/Original/2104703e3391332a38aa)  
![](http://download.broadview.com.cn/Original/2104d7cf12679f955781)

### 第六章 函数

![](http://download.broadview.com.cn/Original/2104216373a442bf4caa)  
![](http://download.broadview.com.cn/Original/2104277b82b8fe5034ae)  
![](http://download.broadview.com.cn/Original/2104c55930ed62025596)  
![](http://download.broadview.com.cn/Original/21046fdf6871f9dcf16d)  
![](http://download.broadview.com.cn/Original/2104273006a1df1de6c6)

### 第七章 类

P228-P273  
类的基本思想是数据抽象和封装。  
抽象是一种依赖于接口和实现分离的编程技术。  
封装实现了类的接口和实现的分离。

![](http://download.broadview.com.cn/Original/2104c9687e9361f617b0)  
![](http://download.broadview.com.cn/Original/2104290d49a45836d575)

![](http://download.broadview.com.cn/Original/2104addd219fef5f55e3)  
![](http://download.broadview.com.cn/Original/2104b5e24e5cc6050d48)  
![](http://download.broadview.com.cn/Original/21043119586dddba9626)  
![](http://download.broadview.com.cn/Original/2104a9b1053fb13a421a)  
![](http://download.broadview.com.cn/Original/21048777c1d7d272bb19)  
![](http://download.broadview.com.cn/Original/210409c8e2051ab58c7c)  
![](http://download.broadview.com.cn/Original/21041107aa248b1b8a83)

### 第八章 IO 库

![](http://download.broadview.com.cn/Original/210426faa8cccad97cce)  
![](http://download.broadview.com.cn/Original/2104d906c590b58d76a0)  
![](http://download.broadview.com.cn/Original/2104e3eed51b32e7c213)

### 第九章 顺序容器

![](http://download.broadview.com.cn/Original/2104a792303f69479227)  
![](http://download.broadview.com.cn/Original/2104b750290253727192)  
![](http://download.broadview.com.cn/Original/2104018fdd1ae73312ad)  
![](http://download.broadview.com.cn/Original/21045096fa2b45f9ea35)  
![](http://download.broadview.com.cn/Original/21041e17ae8babac039c)  
![](http://download.broadview.com.cn/Original/2104222df62b30d142f2)  
![](http://download.broadview.com.cn/Original/21040cb6f5e8d08702f5)  
![](http://download.broadview.com.cn/Original/2104a57aaf211c2f209c)  
![](http://download.broadview.com.cn/Original/210435038497fb982706)  
![](http://download.broadview.com.cn/Original/2104f69c7cfc4cfec579)  
![](http://download.broadview.com.cn/Original/21049d2f03212527e9a5)  
![](http://download.broadview.com.cn/Original/2104fb0193377910912d)  
![](http://download.broadview.com.cn/Original/210491020b164bc66a51)  
![](http://download.broadview.com.cn/Original/210403a2d101c178507c)  
![](http://download.broadview.com.cn/Original/21048b84ac311ecc66c9)

### 第十章 泛型算法

![](http://download.broadview.com.cn/Original/210404f617fcc7b8fbab)  
![](http://download.broadview.com.cn/Original/2104b3c2fc1990ecdcf9)  
![](http://download.broadview.com.cn/Original/2104df30e8f984bc206f)  
![](http://download.broadview.com.cn/Original/210411d55e93072a585a)  
![](http://download.broadview.com.cn/Original/2104e1ab7a9686798e05)  
![](http://download.broadview.com.cn/Original/2104e83fc6cf2ba9774a)  
![](http://download.broadview.com.cn/Original/21041850ec43fca9cca9)

### 第十一章 关联容器

![](http://download.broadview.com.cn/Original/2104426aa8935d26fa4f)  
![](http://download.broadview.com.cn/Original/210452f816826580173d)  
![](http://download.broadview.com.cn/Original/2104e06573e052633c3f)  
![](http://download.broadview.com.cn/Original/2104adde409effead33b)  
![](http://download.broadview.com.cn/Original/2104fdcead734736829a)  
![](http://download.broadview.com.cn/Original/2104692b3c4f521b0a56)

### 第十二章 动态内存

![](http://download.broadview.com.cn/Original/21045624c66b18f0e9f7)  
![](http://download.broadview.com.cn/Original/21044a9d0426ec9ee7bf)  
![](http://download.broadview.com.cn/Original/210470269f5857941917)  
![](http://download.broadview.com.cn/Original/21049a2d129d89df4da0)  
![](http://download.broadview.com.cn/Original/210487334f41c228cb58)  
![](http://download.broadview.com.cn/Original/2104dd2c0b4059d8677b)

### 第十三章 拷贝控制

![](http://download.broadview.com.cn/Original/2104833f257e7e486a37)  
![](http://download.broadview.com.cn/Original/210456f0ad2a4432813c)  
![](http://download.broadview.com.cn/Original/21040ecceaa671de8121)  
![](http://download.broadview.com.cn/Original/210403260fd183ed438b)

##### 13.3 交换操作

swap

##### 13.4 拷贝控制示例

P460

##### 13.5 动态内存管理类

P464

![](http://download.broadview.com.cn/Original/21048c3752fbba15a84a)  
![](http://download.broadview.com.cn/Original/210465b3f4c966a003a6)

### 第十四章 重载运算与类型转换

![](http://download.broadview.com.cn/Original/21044733b4f451c31da6)  
![](http://download.broadview.com.cn/Original/2104f5c59e9127cd356b)  
![](http://download.broadview.com.cn/Original/210476b80a9f2aaa4ef6)  
![](http://download.broadview.com.cn/Original/210476a639c0b85e1483)  
![](http://download.broadview.com.cn/Original/2104ec9df44b1773f6f0)  
![](http://download.broadview.com.cn/Original/21047c80bf1239e924a8)

### 第十五章 面向对象程序设计

![](http://download.broadview.com.cn/Original/21044f357e60988b0e7f)  
![](http://download.broadview.com.cn/Original/2104f72b072c7748915d)  
![](http://download.broadview.com.cn/Original/210434e0149fb34b985b)  
![](http://download.broadview.com.cn/Original/2104d2af5b1e7fced402)  
![](http://download.broadview.com.cn/Original/2104f9f4a3263f0e61b9)  
![](http://download.broadview.com.cn/Original/210440e82b9c6abd908b)  
![](http://download.broadview.com.cn/Original/210474a6fe5dc7d60f99)  
![](http://download.broadview.com.cn/Original/2104753d307b2ca63fdb)  
![](http://download.broadview.com.cn/Original/2104ea4e8124279074fe)

### 第十六章 模板与泛型编程

P578-P630  
（1）控制实例化  
当编译器遇到 extern 模板声明时，它不会在本文件中生成实例化代码。将一个实例化声明为 extern 就表示承诺在程序其他位置有该实例化的一个非 extern 声明（定义）。对于一个给定的实例化版本，可能有多个 extern 声明，但必须只有一个定义。  
（2）模板是标准库的基础。  
生成特定类或者函数的过程称为实例化。  
（3）术语  
类模板：模板定义，可从它实例化出特定的类。类模板的定义以关键词 template 开始，后面跟尖括号对 <和>，其内为一个用逗号分隔的一个或多个模板参数的列表，随后是类的定义。  
函数模板：模板定义，可从它实例化出特定函数。函数模板的定义以关键词 template 开始，后跟尖括号 <和>，其内以一个用逗号分隔的一个或多个模板参数的列表，随后是函数的定义。

![](http://download.broadview.com.cn/Original/2104b96916bbc0dbf557)

**《C++ Primer 中文版（第 5 版）》**  
[美] Stanley B. Lippman,Josee Lajoie,Barbara E. Moo 著  
王刚 杨巨峰 译

**如果只读一本 C++ 书，本书将是你永不局悔的选择**  
**征服全球数千万读者的大师之作**  
**C++ 学习头牌，技术影响力图书冠军**

如果只读一本 C++ 书籍，相信所有读过的人都会毫不犹豫选择《C++ Primer（中文版）（第 5 版）》，无论初学或老手既可用来理解 C++11 规则背后的原理，也可用其新语言特性与标准库来快速构建健壮程序，采用中文版页码全面呈现原书大量交叉引用及详尽索引，新式辅学设置与课后操练用于避开陷阱、巩固良法！  
![](http://download.broadview.com.cn/Original/21042df5414e0318b7cc)  
（扫码了解本书详情）

**扫码进入本书交流群**  
**与广大群友共同学习成长**  
![](http://download.broadview.com.cn/Original/21040ddf1023af1f73ad)