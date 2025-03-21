> 本文由 [简悦 SimpRead](http://ksria.com/simpread/) 转码， 原文地址 [mp.weixin.qq.com](https://mp.weixin.qq.com/s/U5AP1f9WKdJCUo4HVqrjxA)

什么是 “沙箱”
========

> 也称作：“沙箱 / 沙盒 / 沙盘”。沙箱是一种安全机制，为运行中的程序提供隔离环境。通常是作为一些来源不可信、具破坏力或无法判定程序意图的程序提供实验之用。沙箱能够安全的执行不受信任的代码，且不影响外部实际代码影响的独立环境。  

有哪些动态执行脚本的场景？
=============

在一些应用中，我们希望给用户提供插入自定义逻辑的能力，比如 Microsoft 的 Office 中的 VBA，比如一些游戏中的 lua 脚本，FireFox 的「油猴脚本」，能够让用户发在可控的范围和权限内发挥想象做一些好玩、有用的事情，扩展了能力，满足用户的个性化需求。  

大多数都是一些客户端程序，在一些在线的系统和产品中也常常也有类似的需求，事实上，在线的应用中也有不少提供了自定义脚本的能力，比如 Google Docs 中的 Apps Script，它可以让你使用 JavaScript 做一些非常有用的事情，比如运行代码来响应文档打开事件或单元格更改事件，为公式制作自定义电子表格函数等等。  

与运行在「用户电脑中」的客户端应用不同，用户的自定义脚本通常只能影响用户自已，而对于在线的应用或服务来讲，有一些情况就变得更为重要，比如「安全」，用户的「自定义脚本」必须严格受到限制和隔离，即不能影响到宿主程序，也不能影响到其它用户。  

另外，有一些牵扯「模板化」的前端框架，如 Vue.js、Venom.js 等都会用到动态代码执行。

JavaScript 中的沙箱实现
=================

零、几个基础知识
--------

### 什么是 constructor

•JavaScript 中 constructor 属性指向创建当前对象的构造函数，该属性是存在原型里的，且是不可靠的 JavaScript 中 constructor 属性 [2]

```
function test() {}const obj = new test();console.log(obj.hasOwnProperty('constructor')); // falseconsole.log(obj.__proto__.hasOwnProperty('constructor')); // trueconsole.log(obj.__proto__ === test.prototype); // trueconsole.log(test.prototype.hasOwnProperty('constructor')); // true/** constructor是不可靠的 */function Foo() {}Foo.prototype = {};const foo = new Foo();console.log(foo.constructor === Object);  // true，可以看出不是Foo了
```

•constructor 也是一种用于创建和初始化 class[3] 创建的对象的特殊方法 Class 构造方法 [4]

几个典型的 constructor：

```
(async function(){})().constructor === Promise// 浏览器环境下this.constructor.constructor === Functionwindow.constructor.constructor === Function// node环境下this.constructor.constructor === Functionglobal.constructor.constructor === Function
```

### **JS Proxy getPrototypeOf()**

`handler.getPrototypeOf()`是一个代理方法，当读取代理对象的原型时，该方法就会被调用。语法：

```
const p = new Proxy(obj, {  getPrototypeOf(target) { // target 被代理的目标对象。  ...  }});
```

当 getPrototypeOf 方法被调用时，this 指向的是它所属的处理器对象，getPrototypeOf 方法的返回值必须是一个对象或者 null。  

在 JavaScript 中，有下面这五种操作（方法 / 属性 / 运算符）可以触发 JS 引擎读取一个对象的原型，也就是可以触发 getPrototypeOf() 代理方法的运行：

•Object.getPrototypeOf()[5]•Reflect.getPrototypeOf()[6]•**proto**[7]•Object.prototype.isPrototypeOf()[8]•instanceof[9]

如果遇到了下面两种情况，JS 引擎会抛出 TypeError[10] 异常：

•getPrototypeOf() 方法返回的不是对象也不是 null。• 目标对象是不可扩展的，且 getPrototypeOf() 方法返回的原型不是**目标对象本身的原型**。

基本用法：

```
const obj = {};const proto = {};const handler = {    getPrototypeOf(target) {        console.log(target === obj);   // true        console.log(this === handler); // true        return proto;    }};var p = new Proxy(obj, handler); // obj是被代理的对象，也就是handler.getPrototypeOf的target参数console.log(Object.getPrototypeOf(p) === proto);    // true
```

  
5 种触发 getPrototypeOf 代理方法的方式：

```
const obj = {};const p = new Proxy(obj, {    getPrototypeOf(target) {        return Array.prototype;    }});console.log(    Object.getPrototypeOf(p) === Array.prototype,  // true    Reflect.getPrototypeOf(p) === Array.prototype, // true    p.__proto__ === Array.prototype,               // true    Array.prototype.isPrototypeOf(p),              // true    p instanceof Array                             // true);
```

  
两种异常的情况：

```
// getPrototypeOf() 方法返回的不是对象也不是 nullconst obj = {};const p = new Proxy(obj, {    getPrototypeOf(target) {        return "foo";    }});Object.getPrototypeOf(p); // TypeError: "foo" is not an object or null// 目标对象是不可扩展的，且 getPrototypeOf() 方法返回的原型不是目标对象本身的原型const obj = Object.preventExtensions({}); // obj不可扩展const p = new Proxy(obj, {    getPrototypeOf(target) {        return {};    }});Object.getPrototypeOf(p); // TypeError: expected same prototype value// 如果对上面的代码做如下的改造就没问题const obj = Object.preventExtensions({}); // obj不可扩展const p = new Proxy(obj, {    getPrototypeOf(target) { // target就是上面的obj        return obj.__proto__; // 返回的是目标对象本身的原型    }});Object.getPrototypeOf(p); // 不报错
```

一、跟浏览器宿主环境一致的沙箱实现
-----------------

### 构建闭包环境

我们知道在 JavaScript 中的作用域（scope）只有**全局作用域**（global scope）、**函数作用域**（function scope）以及从 ES6 开始才有的**块级作用域**（block scope）。如果要将一段代码中的变量、函数等的定义隔离出来，受限于 JavaScript 对作用域的控制，只能将这段代码封装到一个 Function 中，通过使用 function scope 来达到作用域隔离的目的。也因为需要这种使用函数来达到作用域隔离的目的方式，于是就有 IIFE（立即调用函数表达式），这是一个被称为 “自执行匿名函数” 的设计模式。

```
(function foo(){    const a = 1;    console.log(a); })();// 无法从外部访问变量   console.log(a) // 抛出错误："Uncaught ReferenceError: a is not defined"
```

当函数变成立即执行的函数表达式时，表达式中的变量不能从外部访问，它拥有独立的词法作用域。不仅避免了外界访问 IIFE 中的变量，而且又不会污染全局作用域，弥补了 JavaScript 在 scope 方面的缺陷。一般常见于写插件和类库时，如 JQuery 当中的沙箱模式

```
(function (window) {    var jQuery = function (selector, context) {        return new jQuery.fn.init(selector, context);    }    jQuery.fn = jQuery.prototype = function () {        //原型上的方法，即所有jQuery对象都可以共享的方法和属性    }    jQuery.fn.init.prototype = jQuery.fn;    window.jQeury = window.$ = jQuery; //如果需要在外界暴露一些属性或者方法，可以将这些属性和方法加到window全局对象上去})(window);
```

当将 IIFE 分配给一个变量，不是存储 IIFE 本身，而是存储 IIFE 执行后返回的结果。

```
const result = (function () {    const name = "张三";    return name;})();console.log(result); // "张三"
```

### 原生浏览器对象的模拟

模拟原生浏览器对象的目的是为了防止闭包环境，操作原生对象，篡改污染原生环境，完成模拟浏览器对象之前我们需要先关注几个不常用的 API。

#### eval

eval 函数可将字符串转换为代码执行，并返回一个或多个值：

```
const b = eval("({name:'张三'})");console.log(b.name);
```

由于 eval 执行的代码可以访问闭包和全局范围，因此就导致了代码注入的安全问题，因为代码内部可以沿着作用域链往上找，篡改全局变量，这是我们不希望的。

```
console.log(eval( this.window === window )); // true
```

补充几个点：

• 性能 & 安全问题，一般不建议在实际业务代码中引入 eval• 辅助异步编程框架的 windjs 大量采用 eval 的写法来辅助编程，引发争议 专访 Wind.js 作者老赵（上）：缘由、思路及发展 [11]• 浏览器环境下，(0, eval)() 比 eval() 的性能要好「目前已经不是了」(0, eval)(‘this’)[12]

```
const times = 1000;const time1 = '直接引用';const time2 = '间接引用';let times1 = times;console.time(time1);while(times1--) {    eval(`199 + 200`);}console.timeEnd(time1);let times2 = times;console.time(time2);while(times2--) {    (0, eval)(`199 + 200`);}console.timeEnd(time2);
```

#### new Function

Function 构造函数创建一个新的 Function 对象。直接调用这个构造函数可用于动态创建函数。

```
new Function ([arg1[, arg2[, ...argN]],] functionBody)
```

**arg1, arg2, ... argN** 被函数使用的参数的名称必须是合法命名的。参数名称是一个有效的 JavaScript 标识符的字符串，或者一个用逗号分隔的有效字符串的列表，例如 “×”，“theValue”，或 “a,b”。  

补充几个点：

•new Function() 性能一般比 eval 要好，很多用到这块的前端框架都是用 new Function() 实现的，比如：Vue.js• 打开浏览器控制台后，new Function() 的性能要慢一倍以上

**functionBody**  
一个含有包括函数定义的 JavaScript 语句的字符串。

```
const sum = new Function('a', 'b', 'return a + b'); console.log(sum(1, 2));//3
```

同样也会遇到和 eval 类似的的安全问题和相对较小的性能问题。

```
let a = 1;function sandbox() {    let a = 2;    return new Function('return a;'); // 这里的 a 指向最上面全局作用域内的 1}const f = sandbox();console.log(f());
```

与 eval 不同的是 Function 创建的函数只能在全局作用域中运行，它无法访问局部闭包变量，它们总是被创建于全局环境，因此在运行时它们只能访问全局变量和自己的局部变量，不能访问它们被 Function 构造器创建时所在的作用域的变量。new Function() 是 eval() 更好替代方案。它具有卓越的性能和安全性，但仍没有解决访问全局的问题。  

#### with

with 是 JavaScript 中一个关键字，扩展一个语句的作用域链。它允许半沙盒执行。那什么叫半沙盒？语句将某个对象添加到作用域链的顶部，如果在沙盒中有某个未使用命名空间的变量，跟作用域链中的某个属性同名，则这个变量将指向这个属性值。如果沒有同名的属性，则将拋出 ReferenceError。

```
// 严格模式下以下代码运行会有问题function sandbox(o) {    with (o){        //a=5;         c=2;        d=3;        console.log(a,b,c,d); // 0,1,2,3 //每个变量首先被认为是一个局部变量，如果局部变量与 obj 对象的某个属性同名，则这个局部变量会指向 obj 对象属性。    }}const f = {    a:0,    b:1}sandbox(f);       console.log(f);console.log(c,d); // 2,3 c、d被泄露到window对象上
```

究其原理，with 在内部使用 in 运算符。对于块内的每个变量访问，它都在沙盒条件下计算变量。如果条件是 true，它将从沙盒中检索变量。否则，就在全局范围内查找变量。但是 with 语句使程序在查找变量值时，都是先在指定的对象中查找。所以对于那些本来不是这个对象的属性的变量，查找起来会很慢，对于有性能要求的程序不适合（JavaScript 引擎会在编译阶段进行数项的性能优化。其中有些优化依赖于能够根据代码的词法进行静态分析，并预先确定所有变量和函数的定义位置，才能在执行过程中快速找到标识符）。with 也会导致数据泄漏 (在非严格模式下，会自动在全局作用域创建一个全局变量)

#### in 运算符

in 运算符能够检测左侧操作数是否为右侧操作数的成员。其中，左侧操作数是一个字符串，或者可以转换为字符串的表达式，右侧操作数是一个对象或数组。

```
const o = {      a : 1,      b : function() {}};console.log("a" in o);  //trueconsole.log("b" in o);  //trueconsole.log("c" in o);  //falseconsole.log("valueOf" in o);  //返回true，继承Object的原型方法console.log("constructor" in o);  //返回true，继承Object的原型属性
```

#### with + new Function

配合 with 用法可以稍微限制沙盒作用域，先从当前的 with 提供对象查找，但是如果查找不到依然还能从更上面的作用域获取，污染或篡改全局环境。

```
function sandbox (src) {    src = 'with (sandbox) {' + src + '}';    return new Function('sandbox', src);}const str = `    let a = 1;     window.;     console.log(a); // 打印：1`;sandbox(str)({});console.log(window.name);//'张三'
```

可以看到，基于上面的方案都多多少少存在一些安全问题：

•eval 是全局对象的一个函数属性，执行的代码拥有着和应用中其它正常代码一样的的权限，它能访问「执行上下文」中的局部变量，也能访问所有「全局变量」，在这个场景下，它是一个非常危险的函数 • 使用 Function 构造器生成的函数，并不会在创建它的上下文中创建闭包，一般在全局作用域中被创建。当运行函数的时候，只能访问自己的本地变量和全局变量，不能访问 Function 构造器被调用生成的上下文的作用域 •with 一样的问题，它首先会在传入的对象中查找对应的变量，如果找不到就会往更上层的全局作用域去查找，所以也避免不了污染或篡改全局环境

那有没有更安全一些的沙箱环境实现呢？

#### 基于 Proxy 实现的沙箱 (ProxySandbox)

ES6 Proxy 用于修改某些操作的默认行为，等同于在语言层面做出修改，属于一种 “元编程”（meta programming）

```
function evalute(code,sandbox) {  sandbox = sandbox || Object.create(null);  const fn = new Function('sandbox', `with(sandbox){return (${code})}`);  const proxy = new Proxy(sandbox, {    has(target, key) {      // 让动态执行的代码认为属性已存在      return true;     }  });  return fn(proxy);}evalute('1+2') // 3evalute('console.log(1)') // Cannot read property 'log' of undefined
```

我们知道无论 eval 还是 function，执行时都会把作用域一层一层向上查找，如果找不到会一直到 global，那么利用 Proxy 的原理就是，让执行了代码在 sandobx 中找的到，以达到「防逃逸」的目的。  

我们前面提到 with 在内部使用 in 运算符来计算变量，如果条件是 true，它将从沙盒中检索变量。理想状态下没有问题，但也总有些特例独行的存在，比如 Symbol.unscopables。  

Symbol 对象的 Symbol.unscopables 属性，指向一个对象。该对象指定了使用 with 关键字时，哪些属性会被 with 环境排除。

```
Array.prototype[Symbol.unscopables]// {//   copyWithin: true,//   entries: true,//   fill: true,//   find: true,//   findIndex: true,//   keys: true// }Object.keys(Array.prototype[Symbol.unscopables])// ['copyWithin', 'entries', 'fill', 'find', 'findIndex', 'keys']
```

上面代码说明，数组有 6 个属性，会被 with 命令排除。  

![](https://mmbiz.qpic.cn/mmbiz_png/qnAibK5Aia2dXqibZdYwAr4IgHiasg0EGUj32R1GPibdPiaGvzydMRAOpp1zGkWzeiaoXnAqpOSvsGFriaiaIctsyUibrEWA/640?wx_fmt=png)

  
由此我们的代码还需要修改如下：

```
function sandbox(code) {    code = 'with (sandbox) {' + code + '}'    const fn = new Function('sandbox', code)    return function (sandbox) {        const sandboxProxy = new Proxy(sandbox, {            has(target, key) {                return true            },            get(target, key) {                if (key === Symbol.unscopables) return undefined                return target[key]            }        })        return fn(sandboxProxy)    }}const test = {    a: 1,    log(){        console.log('11111')    }}const code = 'log(); console.log(a)' // 1111,TypeError: Cannot read property 'log' of undefinedsandbox(code)(test)
```

Symbol.unscopables 定义对象的不可作用属性。Unscopeable 属性永远不会从 with 语句中的沙箱对象中检索，而是直接从闭包或全局范围中检索。  

#### 快照沙箱 (SnapshotSandbox)

快照沙箱实现来说比较简单，主要用于不支持 Proxy 的低版本浏览器，原理是基于 diff 来实现的, 在子应用激活或者卸载时分别去通过快照的形式记录或还原状态来实现沙箱，snapshotSandbox 会污染全局 window。  
我们看下 qiankun[13] 的 snapshotSandbox 的源码，这里为了帮助理解做部分精简及注释。

```
function iter(obj, callbackFn) {    for (const prop in obj) {        if (obj.hasOwnProperty(prop)) {            callbackFn(prop);        }    }}/** * 基于 diff 方式实现的沙箱，用于不支持 Proxy 的低版本浏览器 */class SnapshotSandbox {    constructor(name) {        this.name = name;        this.proxy = window;        this.type = 'Snapshot';        this.sandboxRunning = true;        this.windowSnapshot = {};        this.modifyPropsMap = {};        this.active();    }    //激活    active() {        // 记录当前快照        this.windowSnapshot = {};        iter(window, (prop) => {            this.windowSnapshot[prop] = window[prop];        });        // 恢复之前的变更        Object.keys(this.modifyPropsMap).forEach((p) => {            window[p] = this.modifyPropsMap[p];        });        this.sandboxRunning = true;    }    //还原    inactive() {        this.modifyPropsMap = {};        iter(window, (prop) => {            if (window[prop] !== this.windowSnapshot[prop]) {                // 记录变更，恢复环境                this.modifyPropsMap[prop] = window[prop];                              window[prop] = this.windowSnapshot[prop];            }        });        this.sandboxRunning = false;    }}let sandbox = new SnapshotSandbox();//test((window) => {    window.name = '张三'    window.age = 18    console.log(window.name, window.age) //    张三,18    sandbox.inactive() //    还原    console.log(window.name, window.age) //    undefined,undefined    sandbox.active() //    激活    console.log(window.name, window.age) //    张三,18})(sandbox.proxy);
```

#### legacySandBox

qiankun 框架 singular 模式下的 proxy 沙箱实现，为了便于理解，这里做了部分代码的精简和注释。

```
//legacySandBoxconst callableFnCacheMap = new WeakMap();function isCallable(fn) {  if (callableFnCacheMap.has(fn)) {    return true;  }  const naughtySafari = typeof document.all === 'function' && typeof document.all === 'undefined';  const callable = naughtySafari ? typeof fn === 'function' && typeof fn !== 'undefined' : typeof fn ===    'function';  if (callable) {    callableFnCacheMap.set(fn, callable);  }  return callable;};function isPropConfigurable(target, prop) {  const descriptor = Object.getOwnPropertyDescriptor(target, prop);  return descriptor ? descriptor.configurable : true;}function setWindowProp(prop, value, toDelete) {  if (value === undefined && toDelete) {    delete window[prop];  } else if (isPropConfigurable(window, prop) && typeof prop !== 'symbol') {    Object.defineProperty(window, prop, {      writable: true,      configurable: true    });    window[prop] = value;  }}function getTargetValue(target, value) {  /*    仅绑定 isCallable && !isBoundedFunction && !isConstructable 的函数对象，如 window.console、window.atob 这类。目前没有完美的检测方式，这里通过 prototype 中是否还有可枚举的拓展方法的方式来判断    @warning 这里不要随意替换成别的判断方式，因为可能触发一些 edge case（比如在 lodash.isFunction 在 iframe 上下文中可能由于调用了 top window 对象触发的安全异常）   */  if (isCallable(value) && !isBoundedFunction(value) && !isConstructable(value)) {    const boundValue = Function.prototype.bind.call(value, target);    for (const key in value) {      boundValue[key] = value[key];    }    if (value.hasOwnProperty('prototype') && !boundValue.hasOwnProperty('prototype')) {      Object.defineProperty(boundValue, 'prototype', {        value: value.prototype,        enumerable: false,        writable: true      });    }    return boundValue;  }  return value;}/** * 基于 Proxy 实现的沙箱 */class SingularProxySandbox {  /** 沙箱期间新增的全局变量 */  addedPropsMapInSandbox = new Map();  /** 沙箱期间更新的全局变量 */  modifiedPropsOriginalValueMapInSandbox = new Map();  /** 持续记录更新的(新增和修改的)全局变量的 map，用于在任意时刻做 snapshot */  currentUpdatedPropsValueMap = new Map();  name;  proxy;  type = 'LegacyProxy';  sandboxRunning = true;  latestSetProp = null;  active() {    if (!this.sandboxRunning) {      this.currentUpdatedPropsValueMap.forEach((v, p) => setWindowProp(p, v));    }    this.sandboxRunning = true;  }  inactive() {    // console.log(' this.modifiedPropsOriginalValueMapInSandbox', this.modifiedPropsOriginalValueMapInSandbox)    // console.log(' this.addedPropsMapInSandbox', this.addedPropsMapInSandbox)    //删除添加的属性，修改已有的属性    this.modifiedPropsOriginalValueMapInSandbox.forEach((v, p) => setWindowProp(p, v));    this.addedPropsMapInSandbox.forEach((_, p) => setWindowProp(p, undefined, true));    this.sandboxRunning = false;  }  constructor(name) {    this.name = name;    const {      addedPropsMapInSandbox,      modifiedPropsOriginalValueMapInSandbox,      currentUpdatedPropsValueMap    } = this;    const rawWindow = window;    //Object.create(null)的方式，传入一个不含有原型链的对象    const fakeWindow = Object.create(null);     const proxy = new Proxy(fakeWindow, {      set: (_, p, value) => {        if (this.sandboxRunning) {          if (!rawWindow.hasOwnProperty(p)) {            addedPropsMapInSandbox.set(p, value);          } else if (!modifiedPropsOriginalValueMapInSandbox.has(p)) {            // 如果当前 window 对象存在该属性，且 record map 中未记录过，则记录该属性初始值            const originalValue = rawWindow[p];            modifiedPropsOriginalValueMapInSandbox.set(p, originalValue);          }          currentUpdatedPropsValueMap.set(p, value);          // 必须重新设置 window 对象保证下次 get 时能拿到已更新的数据          rawWindow[p] = value;          this.latestSetProp = p;          return true;        }        // 在 strict-mode 下，Proxy 的 handler.set 返回 false 会抛出 TypeError，在沙箱卸载的情况下应该忽略错误        return true;      },      get(_, p) {        //避免使用 window.window 或者 window.self 逃离沙箱环境，触发到真实环境        if (p === 'top' || p === 'parent' || p === 'window' || p === 'self') {          return proxy;        }        const value = rawWindow[p];        return getTargetValue(rawWindow, value);      },      has(_, p) { //返回boolean        return p in rawWindow;      },      getOwnPropertyDescriptor(_, p) {        const descriptor = Object.getOwnPropertyDescriptor(rawWindow, p);        // 如果属性不作为目标对象的自身属性存在，则不能将其设置为不可配置        if (descriptor && !descriptor.configurable) {          descriptor.configurable = true;        }        return descriptor;      },    });    this.proxy = proxy;  }}let sandbox = new SingularProxySandbox();((window) => {  window.name = '张三';  window.age = 18;  window.sex = '男';  console.log(window.name, window.age,window.sex) //    张三,18,男  sandbox.inactive() //    还原  console.log(window.name, window.age,window.sex) //    张三,undefined,undefined  sandbox.active() //    激活  console.log(window.name, window.age,window.sex) //    张三,18,男})(sandbox.proxy); //test
```

legacySandBox 还是会操作 window 对象，但是他通过激活沙箱时还原子应用的状态，卸载时还原主应用的状态来实现沙箱隔离，同样会对 window 造成污染，但是性能比快照沙箱好，不用遍历 window 对象。

#### proxySandbox(多例沙箱)

在 qiankun 的沙箱 proxySandbox 源码里面是对 fakeWindow 这个对象进行了代理，而这个对象是通过 createFakeWindow 方法得到的，这个方法是将 window 的 document、location、top、window 等等属性拷贝一份，给到 fakeWindow。

源码展示：

```
function createFakeWindow(global: Window) {  // map always has the fastest performance in has check scenario  // see https://jsperf.com/array-indexof-vs-set-has/23  const propertiesWithGetter = new Map<PropertyKey, boolean>();  const fakeWindow = {} as FakeWindow;  /*   copy the non-configurable property of global to fakeWindow   see https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Proxy/handler/getOwnPropertyDescriptor   > A property cannot be reported as non-configurable, if it does not exists as an own property of the target object or if it exists as a configurable own property of the target object.   */  Object.getOwnPropertyNames(global)    .filter((p) => {      const descriptor = Object.getOwnPropertyDescriptor(global, p);      return !descriptor?.configurable;    })    .forEach((p) => {      const descriptor = Object.getOwnPropertyDescriptor(global, p);      if (descriptor) {        const hasGetter = Object.prototype.hasOwnProperty.call(descriptor, 'get');        /*         make top/self/window property configurable and writable, otherwise it will cause TypeError while get trap return.         see https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Proxy/handler/get         > The value reported for a property must be the same as the value of the corresponding target object property if the target object property is a non-writable, non-configurable data property.         */        if (          p === 'top' ||          p === 'parent' ||          p === 'self' ||          p === 'window' ||          (process.env.NODE_ENV === 'test' && (p === 'mockTop' || p === 'mockSafariTop'))        ) {          descriptor.configurable = true;          /*           The descriptor of window.window/window.top/window.self in Safari/FF are accessor descriptors, we need to avoid adding a data descriptor while it was           Example:            Safari/FF: Object.getOwnPropertyDescriptor(window, 'top') -> {get: function, set: undefined, enumerable: true, configurable: false}            Chrome: Object.getOwnPropertyDescriptor(window, 'top') -> {value: Window, writable: false, enumerable: true, configurable: false}           */          if (!hasGetter) {            descriptor.writable = true;          }        }        if (hasGetter) propertiesWithGetter.set(p, true);        // freeze the descriptor to avoid being modified by zone.js        // see https://github.com/angular/zone.js/blob/a5fe09b0fac27ac5df1fa746042f96f05ccb6a00/lib/browser/define-property.ts#L71        rawObjectDefineProperty(fakeWindow, p, Object.freeze(descriptor));      }    });  return {    fakeWindow,    propertiesWithGetter,  };}
```

proxySandbox 由于是拷贝复制了一份 fakeWindow，不会污染全局 window，同时支持多个子应用同时加载。  
详细源码请查看：proxySandbox[14]

二、Node.js 中的沙箱实现
----------------

### VM

VM 是 Node.js 默认提供的一个内建模块，VM 模块提供了一系列 API 用于在 V8 虚拟机环境中编译和运行代码。JavaScript 代码可以被编译并立即运行，或编译、保存然后再运行。

```
const vm = require('vm');const script = new vm.Script('m + n'); // 先new一个脚本执行的容器实例const sandbox = { m: 1, n: 2 }; const context = new vm.createContext(sandbox); // 实例化一个执行上下文const res = script.runInContext(context); // 运行console.log(res); // 打印：3
```

执行上这的代码就能拿到结果 3，同时，通过 vm.Script 还能指定代码执行的「最大毫秒数」，超过指定的时长将终止执行并抛出一个异常：

```
try {  const script = new vm.Script('while(true){}',{ timeout: 50 });  ....} catch (err){  // 执行超过了50ms会打印超时的 log  console.log(err.message);}
```

上面的脚本执行将会失败，被检测到超时并抛出异常，然后被 Try Cache 捕获到并打出 log，但同时需要注意的是 vm.Script 的 timeout 选项「只针对同步代有效」，而不包括是异步调用的时间，比如

```
const script = new vm.Script('setTimeout(()=>{},2000)',{ timeout: 50 });
```

上述代码，并不是会在 50ms 后抛出异常，因为 50ms 上边的代码同步执行肯定完了，而 setTimeout 所用的时间并不算在内，也就是说 vm 模块没有办法对异步代码直接限制执行时间。我们也不能额外通过一个 timer 去检查超时，因为检查了执行中的 vm 也没有方法去中止掉。  

另外，在 Node.js 通过 vm.runInContext 看起来似乎隔离了代码执行环境，但实际上却很容易「逃逸」出去。我们看下这个过程。  

使用 VM 模块我们可以在独立的环境中运行不受信任的代码，这就意味着运行在沙箱里的代码不能访问 Node 进程了，对吗？  

基本的使用示例代码：

```
"use strict";const vm = require("vm");const xyz = vm.runInNewContext(`let a = "welcome!";a;`);console.log(xyz); // a
```

现在我们尝试访问进程

```
"use strict";const vm = require("vm");const xyz = vm.runInNewContext(`process`);console.log(xyz);
```

![](https://mmbiz.qpic.cn/mmbiz_png/qnAibK5Aia2dXqibZdYwAr4IgHiasg0EGUj3aXRHatOvBcO18dB6JWFETCddlIuVxy4uFCQYTcsUBBlRnURiaZ3hQuQ/640?wx_fmt=png)

  
“process is not defined”，所以默认情况下 VM 模块不能访问主进程，如果想要访问需要指定授权。  
看起来默认不能访问 “process、require” 等就满足需求了，但是真的就没有办法触及主进程并执行代码了？  
看下面的例子：

```
"use strict";const vm = require("vm");const xyz = vm.runInNewContext(`this.constructor.constructor('return this.process.env')()`);console.log(xyz);
```

> 在 javascript 中 this 指向它所属的对象，所以我们使用它时就已经指向了一个 VM 上下文之外的对象。那么访问 this 的. constructor 就返回 Object Constructor ，访问 Object Constructor 的 .constructor 返回 Function constructor 。  
> Function constructor 就像 javascript 提供的最高函数，他可以访问全局，所以他能返回全局事物。Function constructor 允许从字符串生成函数，从而执行任意代码。  
> 所以我们可以使用 Function constructor 返回主进程。关于 Function constructor 更多内容在这里 [15] 和这里 [16]。

可以正常打印，也就是说顺利拿到了主进程的 process，也就是上面所说的产生了「逃逸」。这招同样对突破 Angular 同样有效 —— AngularJS 沙箱 [17]。  

再看下面的例子：

```
const vm = require('vm');const sandbox = {};const script = new vm.Script('this.constructor.constructor("return process")().exit()');const context = vm.createContext(sandbox);script.runInContext(context);
```

执行上边的代码，宿主程序立即就会「退出」，sandbox 是在 VM 之外的环境创建的，需 VM 中的代码的 this 指向的也是 sandbox，那么

```
//this.constructor 就是外所的 Object 构建函数const ObjConstructor = this.constructor; //ObjConstructor 的 constructor 就是外包的 Functionconst Function = ObjConstructor.constructor;//创建一个函数，并执行它，返回全局 process 全局对象const process = (new Function('return process'))(); //退出当前进程process.exit();
```

没有人愿意用户一段脚本就能让应用挂掉吧。除了退出进程序之外，实际上还能干更多的事情。  
有个简单的方法就能避免通过 this.constructor 拿到 process，如下：

```
const vm = require('vm');//创建一外无 proto 的空白对象作为 sandbox// const sandbox = {}; // 能通过this.constructor 拿到 processconst sandbox = Object.create(null); // 这样就能防止this.constructor 拿到 processconst script = new vm.Script('this.constructor.constructor("return process")()');const context = vm.createContext(sandbox);const nodeProcess = script.runInContext(context);console.log(nodeProcess);
```

但还是有风险的，由于 JavaScript 本身的动态的特点，各种黑魔法防不胜防。事实 Node.js 的官方文档中也提到「 不要把 VM 当做一个安全的沙箱，去执行任意非信任的代码」。

### VM2

在社区中有一些开源的模块用于运行不信任代码，例如 sandbox、vm2、jailed 等。相比较而言 vm2 对各方面做了更多的安全工作，相对安全些。「这也是为什么 imageCook 采用了该沙箱模块」  

从 vm2 的官方 README 中可以看到，它基于 Node.js 内建的 VM 模块，来建立基础的沙箱环境，然后同时使用上了文介绍过的 ES6 的 Proxy 技术来防止沙箱脚本逃逸。  

用同样的测试代码来试试 vm2：

```
const { VM } = require('vm2');new VM().run('this.constructor.constructor("return process")().exit()');
```

如上代码，**并没有成功结束掉宿主程序**，vm2 官方 REAME 中说「vm2 是一个沙盒，可以在 Node.js 中安全的执行不受信任的代码」。  

![](https://mmbiz.qpic.cn/mmbiz_png/qnAibK5Aia2dXqibZdYwAr4IgHiasg0EGUj3N8RXMUft7hhTswOJQSLNYuBE2dZpS09b6rLI6YqOrqKhghKPibggJSg/640?wx_fmt=png)

  
然而，事实上我们还是可以干一些「坏」事情，比如：

> 只要能干坏事情，就是不安全的

```
const { VM } = require('vm2');const vm = new VM({ timeout: 1000, sandbox: {}});vm.run('new Promise(()=>{})');
```

上边的代码将**永远不会执行结束**，如同 Node.js 内建模块一样，vm2 的 timeout 对异步操作是无效的。同时，vm2 也不能额外通过一个 timer 去检查超时，因为它也没有办法将执行中的 vm 终止掉。这会一点点耗费完服务器的资源，让你的应用挂掉。  

那么或许你会想，我们能不能在上边的 sandbox 中放一个假的 Promise 从而禁掉 Promise 呢？答案是能提供一个「假」的 Promise，但却没有办法完成禁掉 Promise，比如

```
const { VM } = require('vm2');const vm = new VM({   timeout: 1000,   sandbox: { Promise: function(){}}});vm.run('Promise = (async function(){})().constructor;new Promise(()=>{});');
```

可以看到通过一行 Promise = (async function(){})().constructor 就可以轻松再次拿到 Promise 了。从另一个层面来看，况且或许有时我们还想让自定义脚本支持异步处理呢。  

关于 VM2 还有更多新的和创新性的绕过 ——更多逃逸 [18]。  

除了从沙箱逃逸，还可以使用 infinite while loop 创建无限循环拒绝服务。

```
const {VM} = require('vm2');new VM({timeout:1}).run(`    function main(){        while(1){}    }        new Proxy({}, {        getPrototypeOf(t){            global.main();        }    })`);
```

### Safeify[19]：Node.js 环境下建立一个更安全的沙箱

通过上文的探究，我们并没有找到一个完美的方案在 Node.js 建立安全的隔离的沙箱。其中 vm2 做了不少处理，相对来讲算是较安全的方案了，但问题也很明显，比如**异步不能检查超时**的问题以及**和宿主程序在相同进程**的问题。  

没有进程隔离时，通过 VM 创建的 sanbox 大体是这样的  

![](https://mmbiz.qpic.cn/mmbiz_png/qnAibK5Aia2dXqibZdYwAr4IgHiasg0EGUj3NzDTgz6FNGEq9Pl3CoqwB70JKrvMxN5nkUIiaYT6zZq91b8cCnvWV9A/640?wx_fmt=png)

  
那么，我们是不是可以尝试，将非受信代码，通过 vm2 这个模块隔离在一个独立的进程中执行呢？然后，执行超时时，直接将隔离的进程干掉，但这里我们需要考虑如下几个问题:

• **通过进程池统一调度管理沙箱进程**

如果来一个执行任务，创建一个进程，用完销毁，仅处理进程的开销就已经稍大了，并且也不能不设限的开新进程和宿主应用抢资源，那么，需要建一个进程池：  
前提：所有任务到来会创建一个 Script 实例，先进入一个 pending 队列，然后直接将 script 实例的 defer 对象返回，调用处就能 await 执行结果了  
然后：由 sandbox master 根据工程进程的空闲程序来调度执行，master 会将 script 的执行信息，包括重要的 ScriptId，发送给空闲的 worker，worker 执行完成后会将「结果 + script 信息」回传给 master，master 通过 ScriptId 识别是哪个脚本执行完毕了，就是结果进行 resolve 或 reject 处理。  

这样，通过「进程池」既能降低「**进程来回创建和销毁的开销**」，也能确保不过度抢占宿主资源；同时，在异步操作超时，还能将工程进程直接杀掉；同时，master 将发现一个工程进程挂掉，会立即创建替补进程。

• **处理的数据和结果公开给沙箱的方法**

进程间如何通讯，需要「动态代码」操作数据后可以直接序列化然后通过 IPC 发送给隔离 Sandbox 进程，执行结果一样经过序列化通过 IPC 传输。  

其中，如果想**公开一个方法给 sandbox**，因为不在一个进程，并不能方便的将一个方案的引用传递给 sandbox。我们可以将宿主的方法，在传递给 sandbox worker 之类做一下处理，转换为一个「描述对象」，包括了允许 sandbox 调用的方法信息，然后将信息，如同其它数据一样发送给 worker 进程，worker 收到数据后，识别出「方法描述对象」，然后在 worker 进程中的 sandbox 对象上建立代理方法，代理方法同样通过 IPC 和 master 通讯。

• **针对沙箱进程进行 CPU 和内存配额限制**

在 Linux 平台，通过 CGroups 对沙箱进程进行整体的 CPU 和内存等资源的配额限制，CGroups 是 Control Groups 的缩写，是 Linux 内核提供的一种可以限制、记录、隔离进程组（Process Groups）所使用的物理资源（如：CPU、Memory、IO 等等）的机制。最初由 Google 的工程师提出，后来被整合进 Linux 内核。CGroups 也是 LXC 为实现虚拟化所使用的资源管理手段，可以说没有 CGroups 就没有 LXC。  

最终，我们建立了一个大约这样的「沙箱环境」  

![](https://mmbiz.qpic.cn/mmbiz_png/qnAibK5Aia2dXqibZdYwAr4IgHiasg0EGUj3SqGicTgZicXJ0gCT3eNjgKvUibTuEickowqCGiabOic0CeOSSeicwWbHa0XAg/640?wx_fmt=png)

  
如此这般处理起来是不是感觉很麻烦？但我们就有了一个更加安全一些的沙箱环境了，基于这些处理被封装为一个独立的模块 Safeify[20]，在 Github 上已经开源。  

相较于内建的 VM 及常见的几个沙箱模块， Safeify 具有如下特点：

• 为将要执行的动态代码建立专门的进程池，与宿主应用程序分离在不同的进程中执行 • 支持配置沙箱进程池的最大进程数量 • 支持限定同步代码的最大执行时间，同时也支持限定包括异步代码在内的执行时间 • 支持限定沙箱进程池的整体的 CPU 资源配额（小数）• 支持限定沙箱进程池的整体的最大的内存限制（单位 m）

简单介绍一下 Safeify 如何使用，通过如下命令安装

```
npm i safeify --save
```

在应用中使用，还是比较简单的，如下代码（TypeScript 中类似）

```
import { Safeify } from 'safeify';const safeVm = new Safeify({  timeout: 50,          //超时时间，默认 50ms  asyncTimeout: 500,    //包含异步操作的超时时间，默认 500ms  quantity: 4,          //沙箱进程数量，默认同 CPU 核数  memoryQuota: 500,     //沙箱最大能使用的内存（单位 m），默认 500m  cpuQuota: 0.5,        //沙箱的 cpu 资源配额（百分比），默认 50%});const context = {  a: 1,   b: 2,  add(a, b) {    return a + b;  }};const rs = await safeVm.run(`return add(a, b)`, context);console.log('result',rs);
```

关于安全的问题，没有最安全，只有更安全。Safeify 已在部分项目中使用，但自定义脚本的功能是往往仅针对内网用户，有不少动态执行代码的场景其实是可以避免的，绕不开或实在需要提供这个功能时，希望本文或 Safeify 能对大家有所帮助就行了。  

### 结论

运行不信任的代码是非常困难的，只依赖软件模块作为沙箱技术，防止不受信任代码用于非正当用途是不得已的决定。这可能促使云上 SAAS 应用的不安全，因为通过逃逸出沙箱进程多个租户间的数据可能被访问（主进程数据获取），这样你就可能可以通过 session，secret 等来潜入其他租户。一个更安全的选择是**依赖于硬件虚拟化**，比如每个租户代码在独立的 docker 容器或 AWS Lambada Function 中执行会是更好的选择。  

下面是 Auth0 如何处理沙箱问题：Sandboxing Node.js with CoreOS and Docker[21]。「下来可以再详细研究下实现」

三、看一个 case
----------

imageCook 的使用 case  

![](https://mmbiz.qpic.cn/mmbiz_png/qnAibK5Aia2dXqibZdYwAr4IgHiasg0EGUj3kNGH18c8dZiaVTUV4yyAp8POtZeTxQvJicv1OCMEvvnARFOicCR765hsA/640?wx_fmt=png)

  
目标：拿到用于前端页面渲染的 index.js ＋ index.css  

基本思路：

• 模板代码生成代码：https://github.com/imgcook-dsl/react-xt-standard/blob/master/src/index.js• 基于 Group / 仓库名可以拿到整个**仓库的所有代码** •gitlab 的代码拉取实现方式可以参考：针对字节现状封装的 Gitlab API[22] 「使用了 Node.js 的混合流」•github 的代码拉取可以参考：https://www.npmjs.com/package/download-git-repo 曾被 vue-cli 2.x[23] 版本使用

```
{    "package.json": "xxx",    "src/index.js": "yyy"}
```

• 拿到执行函数字符串

```
module.exports = function(schema, option) {  let imgNumber = 0;  const {prettier} = option;  ...  };
```

•Node.js 沙箱执行，得到上面函数返回的字符串

```
import { Safeify } from 'safeify';import { getRepoProjectEntries } from 'byte-gitlab';const safeVm = new Safeify({  timeout: 50,          // 超时时间，默认 50ms  asyncTimeout: 500,    // 包含异步操作的超时时间，默认 500ms  quantity: 4,          // 沙箱进程数量，默认同 CPU 核数  memoryQuota: 500,     // 沙箱最大能使用的内存（单位 m），默认 500m  cpuQuota: 0.5,        // 沙箱的 cpu 资源配额（百分比），默认 50%});const context = {   schema: {},    option: {}};(async () => {  const zipStream = await getRepoProjectEntries({    group: 'mordor',    project: 'lynx-standard',    branch: 'master'  });    zipStream    .pipe(async (contents: string, path: string) => {        const rs = await safeVm.run(contents, context);        console.log('result', rs);                return rs;    })    .pipe(this.emitDone())    .once("done", done)    .once("error", (err) => {      console.log("流执行出错统一监控：".red, err);    });})();
```

• 返回给客户端

关于 CSS 隔离
=========

常见的有，不再赘述：•CSS Module•namespace•Dynamic StyleSheet•css in js•Shadow DOM

#### 引用链接

`[1]` 沙箱（Sandbox）: _http://www.arkteam.net/?p=2967_  
`[2]` JavaScript 中 constructor 属性: _https://segmentfault.com/a/1190000013245739_  
`[3]` class: _https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/class_  
`[4]` Class 构造方法: _https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Classes/constructor_  
`[5]` Object.getPrototypeOf(): _https://www.axihe.com/api/js-es/ob-object/get-prototype-of.html_  
`[6]` Reflect.getPrototypeOf(): _https://www.axihe.com/api/js-es/ob-reflect/get-prototype-of.html_  
`[7]` **proto**: _https://www.axihe.com/api/js-es/ob-object/proto.html_  
`[8]` Object.prototype.isPrototypeOf(): _https://www.axihe.com/api/js-es/ob-object/is-prototype-of.html_  
`[9]` instanceof: _https://www.axihe.com/api/js-es/ex-relational/instanceof.html_  
`[10]` TypeError: _https://www.axihe.com/api/js-es/ob-error/type-error.html_  
`[11]` 专访 Wind.js 作者老赵（上）：缘由、思路及发展: _https://www.infoq.cn/article/interview-jscex-author-part-1_  
`[12]` (0, eval)(‘this’): _https://www.cnblogs.com/qianlegeqian/p/3950044.html_  
`[13]` qiankun: _https://qiankun.umijs.org/zh/guide_  
`[14]` ：proxySandbox: _https://link.segmentfault.com/?enc=Mb%2BNNJjUrmTA7g2uf%2FTgzQ%3D%3D.IHwAeHwf8%2FPDd3WJLo%2F4dCWf2md2lzw7s%2BIEdUcUHmX7xMSccEguXX%2BFQBtpgU8SHiyqxgnCi00SvzmT95eNTRD1XaOHjO5xokQrsy%2BHYtQ%3D_  
`[15]` 这里: _https://link.juejin.cn/?target=http%3A%2F%2Fdfkaye.github.io%2F2014%2F03%2F14%2Fjavascript-eval-and-function-constructor%2F_  
`[16]` 这里: _https://link.juejin.cn/?target=https%3A%2F%2Fgithub.com%2Fi0natan%2Fnodebestpractices%2Fissues%2F211_  
`[17]` AngularJS 沙箱: _https://link.juejin.cn/?target=https%3A%2F%2Fportswigger.net%2Fresearch%2Fdom-based-angularjs-sandbox-escapes_  
`[18]` 更多逃逸: _https://github.com/patriksimek/vm2/issues?q=is%3Aissue+author%3AXmiliaH+is%3Aclosed_  
`[19]` Safeify: _https://github.com/Houfeng/safeify_  
`[20]` Safeify: _https://github.com/Houfeng/safeify_  
`[21]` Sandboxing Node.js with CoreOS and Docker: _https://link.juejin.cn/?target=https%3A%2F%2Fwww.youtube.com%2Fwatch%3Fv%3Du81pS05W1JY_

- END -

![](https://mmbiz.qpic.cn/mmbiz_gif/xsw6Lt5pDCu1rRLicXibOB6jq4wpe7W4Ioibu7XTJR1ABzARKoLxyWEWeIV6HJRII2GK1ntnCkVIqjY852gntBd5Q/640?wx_fmt=gif&wxfrom=5&wx_lazy=1)

**敬请关注「Nodejs 技术栈」微信公众号，期望与志同道合的你一起打造优质 “Nodejs 技术栈” 交流群，一起互相学习进步！可长按下方二维码添加【五月君】个人微信备注 “Node” 邀请入群。**

![](https://mmbiz.qpic.cn/mmbiz_png/zPkNS9m6iatLmT5coKbicuqENgoc3Pz4QWwtrEoP2RU2thicCJHaKNmJ23Hh9jYvicpVgiauY6NxNaZ59D6svw1Qskg/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)