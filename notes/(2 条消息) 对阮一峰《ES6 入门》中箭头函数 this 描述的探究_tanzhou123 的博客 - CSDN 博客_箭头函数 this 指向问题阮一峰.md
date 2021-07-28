> 本文由 [简悦 SimpRead](http://ksria.com/simpread/) 转码， 原文地址 [blog.csdn.net](https://blog.csdn.net/tanzhou123/article/details/105919159/)

前言
--

昨日，发了一篇公众号文章：

原文链接：[别低估自己，但，这道题，真的有点难](https://mp.weixin.qq.com/s/1iw1MBfitockO5U0ZJIeXQ)

在部分群里引起了一些讨论，其中有一点是关于箭头函数的 this 指针的问题。使用了阮一峰《ES6 入门》文章的内容来反驳。

为了隐私，屏蔽掉了微信昵称：

![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91c2VyLWdvbGQtY2RuLnhpdHUuaW8vMjAyMC80LzIxLzE3MTk4NWNkMDc3NWQzM2U?x-oss-process=image/format,png)

ryf_group

上述截图，来自阮一峰的《ECMAScript 6 入门》：

![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91c2VyLWdvbGQtY2RuLnhpdHUuaW8vMjAyMC80LzIxLzE3MTk4NWNkMGFmZTJmY2U?x-oss-process=image/format,png)

this

下面我们就来看看箭头函数的 this 到底是啥样的，怎么理解文中的内容！

一道题引发的灾难
--------

![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91c2VyLWdvbGQtY2RuLnhpdHUuaW8vMjAyMC80LzIxLzE3MTk4NWNkMDc5YzI0Yzg?x-oss-process=image/format,png)![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91c2VyLWdvbGQtY2RuLnhpdHUuaW8vMjAyMC80LzIxLzE3MTk4NWNkMDc5MmIzOGY?x-oss-process=image/format,png)

起初，群里一个朋友抛出了这个疑问，为啥这两个输出有差异。

一个是空 person，一个是有值的 person 呢？

那么我们首先就来分析一下到底是什么原因。

普通函数的 getval
------------

let pp = new person("251");

这里创建了一个 person 实例 pp。然后执行 pp.getval()。

pp 调用 getval，符合之前文章提到的 [谁调用，指向谁] 逻辑，所以 getval 的 this 指向 pp 实例，打印出 pp 实例内容。

这个看起来没有人有疑问，一切都很美好。

箭头函数的 getval
------------

同样，let pp = new person("251"); 这里创建了一个 pp 实例，然后执行 pp.getval()。

那么这里箭头函数的 this 指向谁呢？

参考文章 [别低估自己，但，这道题，真的有点难](https://mp.weixin.qq.com/s/1iw1MBfitockO5U0ZJIeXQ)

箭头函数的 this ：父级指向谁，当前箭头函数就指向谁。

这里问题来了， 这里 getval 的箭头函数的父级是谁呢？

从词法作用域来看，可以看到 getval 的父级是 person 函数返回的对象 o，但是 o 对象不是一个函数作用域，没有 this 上下文，当然 getval 函数也无法指向 o 对象的上下文。

按照规则，我们继续往上查找 this，于是找到了 person 函数！

大功告成，getval 的 this 指向 person 函数的 this，所以 getval 的 this 与 person 函数 this 是完全一致的。（实际上箭头函数的 this 就是一个普通变量，指向了 父级的 this）

那么问题来了， person 函数的 this 又是指向谁呢？

我们增加点 log 来加强理解，代码如下：

```
var flag=996;
function person(fg){
    let o = new Object();
    o.flag = fg;
    o.getval=()=>{
        console.log(this);
    }
    this.a = 1;
    console.log("^^^^^^");
    console.log(this);
    console.log("^^^^^^");
    return o;
}
var pp = new person("251")
pp.getval();
console.log("&&&&&&&");
console.log(pp);
console.log("&&&&&&&");
 
// 输出结果如下：
^^^^^^
person {a: 1}
^^^^^^
person {a: 1}
&&&&&&&
{flag: "251", getval: ƒ}
&&&&&&&
```

这里 getval 函数是箭头函数，我们知道，始终与父级的 person 的 this 保持一致，这里 person 的 this 上下文设置了 this.a = 1，所以打印了 {a:1}。

而 person 函数里返回的对象，这里在执行 new 操作符的时候，使用了 person 返回的对象 o。

于是，我们看到输出 pp 实例，完全输出了 person 函数的返回对象内容： {flag: "251", getval: ƒ}。

这里我们有个疑问： person 的 this 与 pp 实例的 this 有啥区别呢？

**这里的关键知识点是：new 操作符**

由于 person 函数返回的是一个对象 (null 除外)，所以在 new 的时候使用了 person 的返回值 o 对象，并没有返回 person 的上下文 this 给到实例 pp。

这里如果 person 函数返回的是一个 [数字、字符串、布尔等]，那么 new 的时候，会忽略返回值，默认返回 person 函数的上下文 this 给到实例对象。

这也是为啥这里输出的 pp 实例不包含 person 函数内定义的 this.a。

而箭头函数的 this 指向 person 的 this ，输出了 this.a=1 但是确不包含 person 函数返回的 o 对象。

**总结：这里箭头函数的 this 永远指向的是父级的 person 的 this，而不是这里的 pp 实例，实际上 箭头函数的 this 就类似一个普通变量，关联上了父级函数的 this**

例子：

```
var flag=996;
function person(fg){
    let o = new Object();
    o.flag = fg;
    o.getval=()=>{
        console.log(this);
    }
    this.a = 1;
    return true;
}
var pp = new person("251");
console.log(pp.a);// 1
```

这里在 new person 的时候，person 函数的返回值不是对象，所以直接忽略。

pp 实例获取的是 person 对象的 this。

箭头函数
----

两个要点：

*   箭头函数中，call 和 apply 会忽略掉 this 参数。(MDN 描述)
    

这其实是 “表象”，实际上是因为箭头函数的 this 是指向父级的 this，因为箭头函数自身没有 this，所以无法修改自身的 this，从而言之 “忽略”。

*   箭头函数的 this ，永远是跟随父级的 this 的。
    

箭头函数的 this 是从当前箭头函数逐级向上查找 this，如果找到了，则作为自己的 this 指向，如果没有则继续向上查找。而父级的 this 是可变的，所以箭头函数的 this 也可跟随父级而改变。

为啥会一直跟随父级的 this 呢？因为实际上箭头函数的 this 就类似一个普通变量，变量的内容就是父级函数的 this，一个变量赋值罢了。

因此，想修改箭头函数 “本身” 的 this 是做不到的。

**但是可以采用变更父级的 this 来达到变更子箭头函数的 this。**

来一个栗子
-----

```
function outer(){
    var inner = function(){
        var obj = {};
        obj.getVal=()=>{
            console.log("*******");
            console.log(this);
            console.log("*******");
        }
        return obj;
    };
    return inner; 
}
outer()().getVal();
// 输出如下
*******
Window {parent: Window, opener: null, top: Window, length: 0, frames: Window, …}
*******
```

getVal 函数是箭头函数，方法里面的 this 是跟着父级的 this。

在 outer() 执行后，返回闭包函数 inner

然后执行闭包函数 inner，而闭包函数的 inner 也是一个普通函数，仍然遵循 [谁调用，指向谁]，这里没有直接调用对象，而是最外层的 “省略的” window 调用的，所以 inner 的 this 是指向 window 的。

闭包函数的作用域与父级的函数作用域是一致的，我们可以理解为闭包函数作用域已经跳出父函数了，但是还可以直接访问父函数内的变量参数等 (这就是闭包的强大之处了)。

这里的 inner 实际上与 outer 的作用域一致。

改变箭头函数的 this
------------

我们可以使用 Bind 改变父级 inner 函数的 this，来达到改变子箭头函数 getVal 的 this 指向。

例子：

```
function outer(){
    var inner = function(){
        var obj = {};
        obj.getVal=()=>{
            console.log("*******");
            console.log(this);
            console.log("*******");
        }
        return obj;
    };
    return inner; 
}
outer().bind(outer)().getVal();
//输出如下
*******
ƒ outer(){
    var inner = function(){
        var obj = {};
        obj.getVal=()=>{
            console.log("*******");
            console.log(this);
            console.log("*******");
        }
  …
*******
```

这里执行 outer 方法，返回 inner 函数。

然后我们改变 inner 的 this 指针，使用 bind 将 inner 的 this 指向到 outer。

我们看到，输出的 this 是 outer 函数。这里我们成功改变了 getVal 的 this 指向。

箭头函数的 this 已经随同父级元素的 this 的改变而改变。（事实上也确实如此，箭头函数的 this 仅仅是父级函数 上下文 this 的赋值）

阮一峰文章中的箭头函数的描述
--------------

我们拷贝原文重点内容如下：

**箭头函数有几个使用注意点。**

**（1）函数体内的 this 对象，就是定义时所在的对象，而不是使用时所在的对象。**

**上面四点中，第一点尤其值得注意。this 对象的指向是可变的，但是在箭头函数中，它是固定的。**

这里表达了两个核心点：

*   函数体内的 this 对象，是定义时所在的对象
    

是的，可以这么理解，箭头函数内的 this 对象，是在定义时，指向词法作用域内的父级对象。

*   this 对象的指向是可变的，但是在箭头函数中，是固定的
    

普通函数的 this 对象是执行是指定的，当然是可变的。箭头函数中 this 是固定的，是固定指向父级上下文 this。

**结论如下：**

箭头函数的 this 是一个普通变量，指向了父级函数的 this，且这个指向永远不会改变，也不能改变。

但是如果需要修改箭头函数的 this ，可以通过修改父级的 this 指针，来达到子箭头函数 this 的修改。（根本原因是箭头函数没有 this，而是在运行时使用父级的 this）。

如有疏漏，欢迎指正~。

>   
> 作者：多点世界  
> 转载文~ 如有侵权请联系删除！！！