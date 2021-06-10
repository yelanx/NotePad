> 本文由 [简悦 SimpRead](http://ksria.com/simpread/) 转码， 原文地址 [www.cnblogs.com](https://www.cnblogs.com/mlw1814011067/p/11283528.html)

熟悉 vue 的人都知道在 vue2.x 之前都是使用 object.defineProperty 来实现双向数据绑定的

而在 vue3.0 中这个方法被取代了

1. 为什么要替换 Object.defineProperty
-------------------------------

**替换不是因为不好，是因为有更好的方法使用效率更高**

Object.defineProperty 的缺点：

1. 在 Vue 中，Object.defineProperty 无法监控到数组下标的变化，

导致直接通过数组的下标给数组设置值，不能实时响应。

[![](http://common.cnblogs.com/images/copycode.gif)](javascript:void(0); "复制代码")

```
push()
pop()
shift()
unshift()
splice()
sort()
reverse()
```

[![](http://common.cnblogs.com/images/copycode.gif)](javascript:void(0); "复制代码")

目前只针对以上方法做了 hack 处理，所以恰数组属性是检测不到的，有局限性。

2. Object.defineProperty 只能劫持对象的属性, 因此我们需要对每个对象的每个属性进行遍历。

Vue 里，是通过递归以及遍历 data 对象来实现对数据的监控的，

如果属性值也是对象那么需要深度遍历, 显然如果能劫持一个完整的对象，不管是对操作性还是性能都会有一个很大的提升。  
而要取代它的 Proxy 有以下两个优点：

```
1. 可以劫持整个对象，并返回一个新对象
2. 有13种劫持操作
```

2. 什么是 Proxy
------------

```
Proxy是 ES6 中新增的一个特性，翻译过来意思是"代理"，用在这里表示由它来“代理”某些操作。 Proxy 让我们能够以简洁易懂的方式控制外部对对象的访问。其功能非常类似于设计模式中的代理模式。

Proxy 可以理解成，在目标对象之前架设一层“拦截”，外界对该对象的访问，都必须先通过这层拦截，因此提供了一种机制，可以对外界的访问进行过滤和改写。

使用 Proxy 的核心优点是可以交由它来处理一些非核心逻辑（如：读取或设置对象的某些属性前记录日志；设置对象的某些属性值前，需要验证；某些属性的访问控制等）。 从而可以让对象只需关注于核心逻辑，达到关注点分离，降低对象复杂度等目的。
```

**基本用法：**

```
let p = new Proxy(target, handler);
```

参数：

```
target: 是用Proxy包装的被代理对象（可以是任何类型的对象，包括原生数组，函数，甚至另一个代理）。
handler: 是一个对象，其声明了代理target 的一些操作，其属性是当执行一个操作时定义代理的行为的函数。
```

p 是 Proxy 对象，当其他操作对 p 进行更改的时候，会执行 handler 对象的方法。Proxy 有 13 种数据劫持的操作，常用的 handler 处理方法：

```
get: 读取值，
set: 获取值，
has: 判断对象是否拥有该属性，
construct: 构造函数
```

**举个例子：**

[![](http://common.cnblogs.com/images/copycode.gif)](javascript:void(0); "复制代码")

```
let obj = {};
 let handler = {
   get(target, property) {
    console.log(`${property} 被读取`);
    return property in target ? target[property] : 3;
   },
   set(target, property, value) {
    console.log(`${property} 被设置为 ${value}`);
    target[property] = value;
   }
 }

 let p = new Proxy(obj, handler);
 p.name = 'tom' //name 被设置为 tom
 p.age; //age 被读取 3
```

[![](http://common.cnblogs.com/images/copycode.gif)](javascript:void(0); "复制代码")

更多的 Proxy 属性方法参考 MDN [Proxy](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Proxy)

3. Proxy 实现数据劫持
---------------

[![](http://common.cnblogs.com/images/copycode.gif)](javascript:void(0); "复制代码")

```
observe(data) {
  const that = this;
  let handler = {
   get(target, property) {
      return target[property];
    },
    set(target, key, value) {
      let res = Reflect.set(target, key, value);
      that.subscribe[key].map(item => {
        item.update();
      });
      return res;
    }
  }
  this.$data = new Proxy(data, handler);
}
```

[![](http://common.cnblogs.com/images/copycode.gif)](javascript:void(0); "复制代码")

这段代码里把代理器返回的对象代理到 this.$data，即 this.$data 是代理后的对象，外部每次对 this.$data 进行操作时，实际上执行的是这段代码里 handler 对象上的方法。  
注：这儿用到了 reflect 属性，这也是 ES6 里面的，不知道的去这儿看看吧。[reflect 属性](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Reflect)