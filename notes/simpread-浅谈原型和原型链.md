> 本文由 [简悦 SimpRead](http://ksria.com/simpread/) 转码， 原文地址 [zhuanlan.zhihu.com](https://zhuanlan.zhihu.com/p/63940964)

好久没写文章了，最近在看一些 js 基础相关的知识，无论是在工作中还是面试时，前端都避不开一个知识，那就是对原型知识的理解。好一些的公司面试官必回问一个问题 --"对 js 原型的理解"。那么今天我就简单的谈谈这块知识，不跟你多 bb，下面是正文了。

**下面先来了解几个规则：**

*   所有的引用类型，都具有对象特性，即可自由扩展属性（除了 “null” 以外）
*   所有的引用类型，都有一个隐式原型__proto__属性，属性值是一个普通的对象
*   所有的引用类型，隐式原型__proto__属性值指向它的构造函数的显式原型 “prototype” 属性值
*   当试图得到一个对象的某个属性时，如果这个对象本身没有这个属性，那么它会去它的隐式原型__proto__（也就是它的构造函数的显式原型 prototype）中寻找。

那么我们来一一的验证上面几个原则，就会慢慢的理解原型和原型链。

1.  **所有的引用类型，都具有对象特性，即可自由扩展属性（除了 “null” 以外）**

```
var obj = {};
var arr = [];
var fn = function() {}

obj.a = 1;
arr.a = 2
fn.a = 3
```

> 这个规则应该比较好理解，额外介绍一个小知识点，var obj ={}; 相当于 var obj = new Object(); 不过在正常的工作业务中，不会去这么定义一个对象，因为不仅麻烦，可读性也会变差。

**2. 所有的引用类型，都有一个隐式原型__proto__属性，属性值是一个普通的对象**

```
var obj = {};
var arr = [];
function fn() {}

console.log(obj.__proto__);
console.log(arr.__proto__);
console.log(fn.__proto__);
```

> 打印出的结果如下图

![](https://pic2.zhimg.com/v2-1851d397b4e1e31fc8d6ea59b8ab45a9_b.jpg)

**3. 所有函数都有一个显式原型 prototype**

![](https://pic1.zhimg.com/v2-f43534e82efcf15119b2354f6cf76c34_b.jpg)

**3. 所有的引用类型，隐式原型__proto__属性值指向它的构造函数的显式原型 “prototype” 属性值**

```
var obj = {};
var arr = [];
function fn() {}

console.log(obj.__proto__ === Object.prototype) //true
console.log(arr.__proto__ === Array.prototype) // true
console.log(fn.__proto__ === Function.prototype) // true
```

**4. 当试图得到一个对象的某个属性时，如果这个对象本身没有这个属性，那么它会去它的__proto__（也就是它的构造函数的显式原型 prototype）中寻找**

```
var obj = {a:1};
obj.toString(); // [object, object]
```

> obj 本身是没有 toString 方法属性的，之所以能获取到这个方法，其实就是遵循了第四条规则，从他的构造函数 Object 的 prototype 中去拿到了这个方法。

**再看一个例子：**

```
function Person(name) {
  this.name = name;
  return this; // 其实这行可以不写，默认不认返回this对象
}

var nick = new Person("nick");
nick.toString(); // [object, object]
```

> 按理说，nick 是构造函数 Person 生成的实例，而它的 prototype 并没有 toString 方法，那为什么 nick 对象能获取到 toString 方法呢？其实这就涉及到原型链了，nick 先去找自身有无 toString 方法属性，找不到那就往上走，找构造函数的 prototype，还是没找到，那么就继续往上，构造函数的 prototype 其实也就是一个对象 (如下图所示)，那么对象的构造函数就是 Object，所以就找到了 Object.prototype 下的 toString 方法

![](https://pic3.zhimg.com/v2-1467b67459f14bca580055fcfb971a0a_b.jpg)

**用一张图片来解释一下上面的流程**

![](https://pic1.zhimg.com/v2-3bee507d110a3ad66d8b5841395b4f58_b.jpg)

最后一个 null，设计上是为了避免死循环而设置的, Object 的隐式原型指向 null。

既然说到了原型，那就说说判断一个引用类型的变量。如下代码

```
function Foo(name) {
  this.name = name;
}
var f = new Foo('nick')

f instanceof Foo // true
f instanceof Object // true
```

instanceof 是通过原型去进行比较对象是否属于当前比较的构造函数，f instanceof Foo 的判断逻辑： f 的隐式原型__proto__一层一层往上，能否对应到 Foo.prototype 同理 f instanceof Object 也是为 true , 因为往上找，也能找到 Object.prototype。

写得不好请见谅。