> 本文由 [简悦 SimpRead](http://ksria.com/simpread/) 转码， 原文地址 [juejin.cn](https://juejin.cn/post/6966781111888265253)

> 1. 输出是什么？ 2. 输出是什么？ 3. 输出是什么？ 4. 输出是什么？ 5. 哪一个是正确的？

1. 输出是什么？
---------

```
function sayHi() {
  console.log(name)
  console.log(age)
  var name = 'Lydia'
  let age = 21
}

sayHi()
复制代码
```

> 答案是：undefined 和报错 因为 var 定义的变量具有变量提升的效果，但是仅仅是变量声明的提升，并未进行赋值，所以是 undefined，let 定义的变量不具备变量提升的效果，所以是报错的。 ![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/506807e62e77477b9c40c3ac861547d2~tplv-k3u1fbpfcp-zoom-1.image)

2. 输出是什么？
---------

```
for (var i = 0; i < 3; i++) {
  setTimeout(() => console.log(i), 1)
}

for (let i = 0; i < 3; i++) {
  setTimeout(() => console.log(i), 1)
}

复制代码
```

> 答案是：333 和 012，因为 JS 的事件循环机制，setTimeout 属于宏任务，要等到同步代码执行完之后才能执行，var 在此处定义的是全局变量，因此同步代码执行完之后 i 已经变成了 3，所以打印 3 个 3，但是 let 定义的变量会形成一个块级作用域，因此是 0 1 2

3. 输出是什么？
---------

```
const shape = {
  radius: 10,
  diameter() {
    return this.radius * 2
  },
  perimeter: () => 2 * Math.PI * this.radius
}

shape.diameter()
shape.perimeter()

复制代码
```

> 答：输出是 20 和 NaN，因为 diameter 中的 this 指的是 shape 中的 radius，但是 perimeter 由于是箭头函数所以，当我们调用 perimeter 时，this 不是指向 shape 对象，而是它的周围作用域（在例子中是 window）。

4. 输出是什么？
---------

```
+true;
!"Lydia";
复制代码
```

> 解：+ 号会将 true 变为 1，+ 号倾向于返回一个值，但是! 倾向于返回一个布尔值，因为 "Lydia" 是真实存在的，所以取反之后是 false。

5. 哪一个是正确的？
-----------

```
const bird = {
  size: 'small'
}
const mouse = {
  name: 'Mickey',
  small: true
}
复制代码
```

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/7095828f8b6a4b268cbda2f1e81edcbe~tplv-k3u1fbpfcp-zoom-1.image)

> 答案：A，因为 mouse 通过点的形式调用 bird 的前提是 mouse 有这个属性，但是它没有，所以是无效的，但是 bird.size 是真实存在的，通过 [] 调用里面存放的是 small 这个字符串，这个和 C 选项是一样的，同理说明 D 的说法是不对的，所以本题选 A。