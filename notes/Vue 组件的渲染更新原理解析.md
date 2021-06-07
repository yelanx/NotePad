> 本文由 [简悦 SimpRead](http://ksria.com/simpread/) 转码， 原文地址 [www.cnblogs.com](https://www.cnblogs.com/ming1025/p/13091678.html)

本版本是对于 vue2.x 的总结，未来等学习了 vue3，再完善对比一波！然后就是说，我们这里主要介绍原理部分，所谓二八原则，我们一切都从最重要的说起吧！

一切从这张图开始

 ![](https://img2020.cnblogs.com/blog/979149/202006/979149-20200611101848175-251562137.png)

让我们一步步看吧！ 

一、初始化

 ![](https://img2020.cnblogs.com/blog/979149/202006/979149-20200611101856353-115056859.png)

在 new Vue() 之后。 Vue 会调用 _init 函数进行初始化，也就是这里的 init 过程，它会初始化生命周期、事件、 props、 methods、 data、 computed 与 watch 等。

二、模板编译

 ![](https://img2020.cnblogs.com/blog/979149/202006/979149-20200611101904038-53608074.png)

上面就是使用 vue template complier（compile 编译可以分成 parse、optimize 与 generate 三个阶段），将模板编译成 render 函数，执行 render 函数后，变成 vnode。

parse、optimize 与 generate 三个阶段

parse

parse 会用正则等方式解析 template 模板中的指令、class、style 等数据，形成 AST，就是 with 语法的过程。

optimize

optimize 的主要作用是标记 static 静态节点，这是 Vue 在编译过程中的一处优化，后面当 update 更新界面时，会有一个 patch 的过程， diff 算法会直接跳过静态节点，从而减少了比较的过程，优化了 patch 的性能。

generate

generate 是将 AST 转化成 render function 字符串的过程，得到结果是 render 的字符串以及 staticRenderFns 字符串。

在经历过 parse、optimize 与 generate 这三个阶段以后，组件中就会存在渲染 VNode 所需的 render function 了。

三、vue 的响应式原理：

 ![](https://img2020.cnblogs.com/blog/979149/202006/979149-20200611101924033-1671233810.png)

前置知识: 

observer (value) ，其中 value（需要「响应式」化的对象）。  
defineReactive ，这个方法通过 Object.defineProperty 来实现对对象的「响应式」化，入参是一个 obj（需要绑定的对象）、key（obj 的某一个属性），val（具体的值）。  
对象被读，就是说，这个值已经在页面中使用或则说已经使用插值表达式插入。  
正式知识: 

 1. 首先我们一开始会进行响应式初始化，也即是我们开始前的哪个 init 过程，通过 observer (value) 方法，然后通过 defineReactive() 方法遍历，对每个对象的每个属性进行 setter 和 getter 初始化。

2. 依赖收集：我们在闭包中增加了一个 Dep 类的对象，用来收集 Watcher 对象。在对象被「读」的时候，会触发 reactiveGetter 函数把当前的 Watcher 对象，收集到 Dep 类中去。之后如果当该对象被「写」的时候，则会触发 reactiveSetter 方法，通知 Dep 类调用 notify 来触发所有 Watcher 对象的 update 方法更新对应视图。

附加知识点：object.defineproperty() 的缺点

我们知道 vue 响应式主要使用的是 object.defineproperty() 这个 api，那他也会带来一些缺点：

需要深度监听，需要递归到底，一次性计算量大（比如引用类型层级较深）

无法监听新增属性 / 删除属性，需要使用 Vue.set 和 Vue.delete 才行  
无法监听原生数组，需要重写数组方法  
 四、虚拟 dom

DOM 操作非常耗时，所以使用 VDOM，我们把计算转移为 JS 计算，  
VDOM - 用 JS 模拟 DOM 结构，计算出最小的变更，操作 DOM  
因为有了虚拟 DOM，所以让 Vue 有了跨平台的能力  
看一道题目: 将下面的东西手写成 vdom/vnode 结构

```
1 <div>
2     <p>vdom</p>
3     <ul></ul>
4 </div>
```

![](https://img2020.cnblogs.com/blog/979149/202006/979149-20200611102015295-395359327.png)

  
vue3 已经重写了 vdom 的代码，优化了性能，但是理念不变！

五、patch 函数，diff 算法上台

 ![](https://img2020.cnblogs.com/blog/979149/202006/979149-20200611102030541-1054459101.png)

这部分涉及算法

前置知识：

insert：在父几点下插入节点，如果指定 ref 则插入道 ref 这个子节点的前面。  
createElm：用来新建一些节点，tag 节点存在创建一个标签节点，否则创建一个文本节点。  
addVnodes：用来批量调用 createElm 新建节点。  
removeNode：用来移除一个节点  
removeVnodes：会批量调用 removeNode 移除节点  
patch 函数：

patch 的核心就是 diff 算法，diff 算法通过同层的树节点进行比较而非对树进行逐层搜索遍历的方式，所以时间复杂度只有 o(n)，比较高效，我们看下图所示：

 ![](https://img2020.cnblogs.com/blog/979149/202006/979149-20200611102112361-370343694.png)

我们看下 patch 这个函数的 demo:

[![](http://common.cnblogs.com/images/copycode.gif)](javascript:void(0); "复制代码")

```
1 function patch (oldVnode, vnode, parentElm) {
 2     if (!oldVnode) {
 3         addVnodes(parentElm, null, vnode, 0, vnode.length - 1);
 4     } else if (!vnode) {
 5         removeVnodes(parentElm, oldVnode, 0, oldVnode.length - 1);
 6     } else {
 7         if (sameVnode(oldVNode, vnode)) {
 8             patchVnode(oldVNode, vnode);
 9         } else {
10             removeVnodes(parentElm, oldVnode, 0, oldVnode.length - 1);
11             addVnodes(parentElm, null, vnode, 0, vnode.length - 1);
12         }
13     }
14 }
```

[![](http://common.cnblogs.com/images/copycode.gif)](javascript:void(0); "复制代码")

  
首先在 oldVnode（老 VNode 节点）不存在的时候，相当于新的 VNode 替代原本没有的节点，所以直接用 addVnodes 将这些节点批量添加到 parentElm 上。  
如果 vnode（新 VNode 节点）不存在的时候，相当于要把老的节点删除，所以直接使用 removeVnodes 进行批量的节点删除即可。  
当 oldVNode 与 vnode 都存在的时候，需要判断它们是否属于 sameVnode（相同的节点）。如果是则进行 patchVnode（比对 VNode ）操作，否则删除老节点，增加新节点   
patchVnode 函数:

我们看下关键代码

[![](http://common.cnblogs.com/images/copycode.gif)](javascript:void(0); "复制代码")

```
1 function patchVnode (oldVnode, vnode) {
 2     // 新老节点相同，直接return
 3     if (oldVnode === vnode) {
 4         return;
 5     }
 6     // 节点是否静态，并且新老接待你的key相同，只要把老节点拿来用就好了
 7     if (vnode.isStatic && oldVnode.isStatic && vnode.key === oldVnode.key) {
 8         vnode.elm = oldVnode.elm;
 9         vnode.componentInstance = oldVnode.componentInstance;
10         return;
11     }
13     const elm = vnode.elm = oldVnode.elm;
14     const oldCh = oldVnode.children;
15     const ch = vnode.children;
16     // 当VNode是文本节点，直接setTextContent来设置text
17     if (vnode.text) {
18         nodeOps.setTextContent(elm, vnode.text);
19     // 不是文本节点
20     } else {
21         // oldch(老)与ch(新)存在且不同，使用updateChildren()
22         if (oldCh && ch && (oldCh !== ch)) {
23             updateChildren(elm, oldCh, ch);
24         // 只有ch存在，若oldch(老)节点是文本节点，先删除，再将ch(新)节点插入elm节点下
25         } else if (ch) {
26             if (oldVnode.text) nodeOps.setTextContent(elm, '');
27             addVnodes(elm, null, ch, 0, ch.length - 1);
28         // 同理当只有oldch(老)节点存在，说明需要将oldch(老)节点通过removeVnode全部删除
29         } else if (oldCh) {
30             removeVnodes(elm, oldCh, 0, oldCh.length - 1)
31         // 当老节点是文本节点，清除其节点内容
32         } else if (oldVnode.text) {
33             nodeOps.setTextContent(elm, '')
34         }
35     }
36 }
```

[![](http://common.cnblogs.com/images/copycode.gif)](javascript:void(0); "复制代码")

  
整理如下：

新老节点相同，直接 return  
节点是否静态，并且新老接待你的 key 相同，只要把老节点拿来用就好了  
当 VNode 是文本节点，直接 setTextContent 来设置 text，若不是文本节点者执行 4-7  
oldch(老) 与 ch(新) 存在且不同，使用 updateChildren()（后面介绍）  
只有 ch 存在，若 oldch(老) 节点是文本节点，先删除，再将 ch(新) 节点插入 elm 节点下  
同理当只有 oldch(老) 节点存在，说明需要将 oldch(老) 节点通过 removeVnode 全部删除  
当老节点是文本节点，清除其节点内容  
updateChildren 函数

下面是关键代码：

 ![](https://img2020.cnblogs.com/blog/979149/202006/979149-20200611102211149-1839156898.png)

直接看我的代码注释吧！

[![](http://common.cnblogs.com/images/copycode.gif)](javascript:void(0); "复制代码")

```
1 // sameVnode() 就是说key，tag，iscomment(注释节点)，data四个同时定义
 2 while (oldStartIdx <= oldEndIdx && newStartIdx <= newEndIdx) {
 3   if (!oldStartVnode) {
 4       oldStartVnode = oldCh[++oldStartIdx];
 5   } else if (!oldEndVnode) {
 6       oldEndVnode = oldCh[--oldEndIdx];
 7   // 老节点的开头与新节点的开头对比
 8   } else if (sameVnode(oldStartVnode, newStartVnode)) {
 9       patchVnode(oldStartVnode, newStartVnode);
10       oldStartVnode = oldCh[++oldStartIdx];
11       newStartVnode = newCh[++newStartIdx];
12   // 老节点的结尾与新节点的结尾对比
13   } else if (sameVnode(oldEndVnode, newEndVnode)) {
14       patchVnode(oldEndVnode, newEndVnode);
15       oldEndVnode = oldCh[--oldEndIdx];
16       newEndVnode = newCh[--newEndIdx];
17   // 老节点的开头与新节点的结尾
18   } else if (sameVnode(oldStartVnode, newEndVnode)) {
19       patchVnode(oldStartVnode, newEndVnode);
20       nodeOps.insertBefore(parentElm, oldStartVnode.elm, nodeOps.nextSibling(oldEndVnode.elm));
21       oldStartVnode = oldCh[++oldStartIdx];
22       newEndVnode = newCh[--newEndIdx];
23   // 老节点的结尾与新节点的开头
24   } else if (sameVnode(oldEndVnode, newStartVnode)) {
25       patchVnode(oldEndVnode, newStartVnode);
26       nodeOps.insertBefore(parentElm, oldEndVnode.elm, oldStartVnode.elm);
27       oldEndVnode = oldCh[--oldEndIdx];
28       newStartVnode = newCh[++newStartIdx];
29   // 如果上面的情况都没有满足
30   } else {
31       // 把老的元素进行移动
32       let elmToMove = oldCh[idxInOld];
33       // 如果老的节点找不到对应索引则创建
34       if (!oldKeyToIdx) oldKeyToIdx = createKeyToOldIdx(oldCh, oldStartIdx, oldEndIdx);
35       // 在新节点中的key值找到老节点索引
36       idxInOld = newStartVnode.key ? oldKeyToIdx[newStartVnode.key] : null;
37       // 如果没有找到相同的节点，则通过 createElm 创建一个新节点，并将 newStartIdx 向后移动一位。
38       if (!idxInOld) {
39           createElm(newStartVnode, parentElm);
40           newStartVnode = newCh[++newStartIdx];
41       // 否则如果找到了节点，同时它符合 sameVnode，则将这两个节点进行 patchVnode，将该位置的老节点赋值 undefined
42       } else {
43           // 这是是想把相同的节点进行移动
44           elmToMove = oldCh[idxInOld];
45           // 然后再进行对比
46           if (sameVnode(elmToMove, newStartVnode)) {
47               patchVnode(elmToMove, newStartVnode);
48               oldCh[idxInOld] = undefined;
49               nodeOps.insertBefore(parentElm, newStartVnode.elm, oldStartVnode.elm);
50               newStartVnode = newCh[++newStartIdx];
51               // 如果不符合 sameVnode，只能创建一个新节点插入到 parentElm 的子节点中，newStartIdx 往后移动一位。
52           } else {
53               createElm(newStartVnode, parentElm);
54               newStartVnode = newCh[++newStartIdx];
55           }
56       }
57   }
58 }
59 // 当oldStartIdx > oldEndIdx 或oldStartIdx> oldEndIdx说明结束
60 if (oldStartIdx > oldEndIdx) {
61   refElm = (newCh[newEndIdx + 1]) ? newCh[newEndIdx + 1].elm : null;
62   addVnodes(parentElm, refElm, newCh, newStartIdx, newEndIdx);
63 } else if (newStartIdx > newEndIdx) {
64   removeVnodes(parentElm, oldCh, oldStartIdx, oldEndIdx);
65 }
66 }
```

[![](http://common.cnblogs.com/images/copycode.gif)](javascript:void(0); "复制代码")

  
上面涉及了很多东西，也不是一时半会能够讲完的，看代码的过程也挺艰辛的！

最后总结下渲染过程

初次渲染：

解析模板为 render 函数 (或再开发环境已完成)  
触发响应式，监听 data 属性的 getter 的依赖收集，也即是往 dep 里面添加 watcher 的过程  
执行 render 函数，生成 vnode，patch  
更新过程:

修改 data，setter(必需是初始渲染已经依赖过的) 调用 Dep.notify()，将通知它内部的所有的 Watcher 对象进行视图更新  
重新执行 rendern 函数，生成 newVnode  
然后就是 patch 的过程 (diff 算法)  
在过不久，vue3 就要到来，很多的 API 也会更爱，像增加 proxy 代理的方式，也更改了编译模板的方式，这大大的影响你的格局，未来将要到来，我将不断奔跑！