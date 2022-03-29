> 本文由 [简悦 SimpRead](http://ksria.com/simpread/) 转码， 原文地址 [mp.weixin.qq.com](https://mp.weixin.qq.com/s/pRXHgL8Ewypu_zIsD_Fq1w)

今天看了下 React 的类型定义，也就是 @types/react 包下的 index.d.ts，发现了一些有趣的写法。

这篇文章就分享下这些写法，估计大部分人都不知道：

提取可选索引的值
--------

首先，我看到了这样一段类型逻辑：

![](https://mmbiz.qpic.cn/mmbiz_png/YprkEU0TtGiayopgibpqvyxjgLfUK65VLgQqkZXiabFIENB7kgJNbib8yjP26IgBvF7wc5FrGMZiceNQOD3SqNtLpiaw/640?wx_fmt=png)

这段逻辑就是取索引类型的 ref 索引的值，但是是通过模式匹配的方式，把提取的类型放到 infer 声明的局部变量 R 里返回的。

简化一下就是这样的：

![](https://mmbiz.qpic.cn/mmbiz_png/YprkEU0TtGiayopgibpqvyxjgLfUK65VLg0qM7zzvyNiaaCDqLz0DOqbf9jLCjFP115mWp4oQfclZd9YDPWI48HjA/640?wx_fmt=png)

提取 Props 的 ref 索引的值的类型返回。

我在想，这么麻烦干什么，直接 Props['ref'] 不就能拿到 ref 索引的值么？

于是我就改成了这样：

![](https://mmbiz.qpic.cn/mmbiz_png/YprkEU0TtGiayopgibpqvyxjgLfUK65VLgGFe7vWsZQpooMhZ0vw1uWlibszjibqoSWichAYLmdiasntY5FbIvaVAtOg/640?wx_fmt=png)

然后试了下：

![](https://mmbiz.qpic.cn/mmbiz_png/YprkEU0TtGiayopgibpqvyxjgLfUK65VLgzibTAia9TAQiaicgzGO4TU975VrYibQlXYKEJOnwibeMo9y9TqgZTMc29j3w/640?wx_fmt=png)

不对呀，人家这是可选索引，值的类型是包含 undefined 的联合类型。

那就 Exclude 下不就行了：

![](https://mmbiz.qpic.cn/mmbiz_png/YprkEU0TtGiayopgibpqvyxjgLfUK65VLgjXMcia94jSVsXYnaBdcDl7zQTTUvmJZmibbo9cc1MjXicnQXvMCyCvO0A/640?wx_fmt=png)

这样也比那个 infer 的方式简洁呀，为啥 React 类型定义都是用的 infer 取的可选索引的类型呢？

后来我突然想到，如果这个 ref 值的类型就是 undefined 呢？

我试了下：

![](https://mmbiz.qpic.cn/mmbiz_png/YprkEU0TtGiayopgibpqvyxjgLfUK65VLglG3MDfZU5cWY34J0eyBoB0CNic6kTFXo2sWvhuvslfkT0ScPRcKy8SQ/640?wx_fmt=png)

确实，我那样写是有问题的，如果值的类型本来就是 undefined，Exclude 掉 undefined 后就是 never 了，而人家那种方式就没问题：

![](https://mmbiz.qpic.cn/mmbiz_png/YprkEU0TtGiayopgibpqvyxjgLfUK65VLgjUbcOZJOJKia1HvMvTvA2LogO6q2yibGNEYMM6IFZOQj4qG5Fjfp5rdA/640?wx_fmt=png)

于是我就加一下 undefined 的处理：

![](https://mmbiz.qpic.cn/mmbiz_png/YprkEU0TtGiayopgibpqvyxjgLfUK65VLgR4yX4QmbdrmIzaSicxiaV3nyf1F3x2ynwEibKNBH0UslnAlRbicOMva8Jg/640?wx_fmt=png)

这样就行了。

对比了下两种写法：

![](https://mmbiz.qpic.cn/mmbiz_png/YprkEU0TtGiayopgibpqvyxjgLfUK65VLgsbL5mjbYNLKp7All79OBw25OIjhSdgc8y0PrHdSC0KJr1CoRaBnibzA/640?wx_fmt=png)

确实还是 React 的那种写法更简洁。

对了，那上面那层判断呢？

![](https://mmbiz.qpic.cn/mmbiz_png/YprkEU0TtGiayopgibpqvyxjgLfUK65VLgxNP2dPxZuvuabztQw5y9Lv7LVfZyFL1dsalsgRYlZoKXPaMBNJwPicQ/640?wx_fmt=png)

这个判断没必要的吧，如果没有 ref，那 Props['ref'] 不就是返回 never 么，没必要单独判断呀？

然后我就看到了这样一段注释：

![](https://mmbiz.qpic.cn/mmbiz_png/YprkEU0TtGiayopgibpqvyxjgLfUK65VLga6GAYfnicE3XLquUt9jYxGM1fx2Xtia1DpnOAJATMBseyQkgdcpdhibbw/640?wx_fmt=png)

在 ts 3.0 中，如果索引类型没有对应的索引，那返回的类型是 {} 而不是 never。

原来如此，这个'ref' extends keyof Props 是为了做兼容的呀，不是没意义。

这就是我从这个类型中学到的两个知识点：

*   **索引访问 Obj[Key] 和 infer 提取和都可以取到索引类型的某个索引的值，但是当处理可选索引的时候，用 infer 更简洁一些，因为前者要取出类型之后再单独处理下 undefined，而后者在 infer 的时候就顺便处理了 undefined。**
    
*   **ts 3.0 中如果索引类型没有对应的索引，返回的是 {} 不是 never，如果对兼容性要求高的话，可以用'xx' in keyOf Obj 的方式做下兼容**
    

我们从这个类型里学到了不少东西，再来看下第二个类型：

索引类型和 any、never 的处理
-------------------

然后我又看到了这样一个类型，

![](https://mmbiz.qpic.cn/mmbiz_png/YprkEU0TtGiayopgibpqvyxjgLfUK65VLgKgSHLXPcoPLsXSuhhcyOsyBOS3lC3MRlhcP1jVqogUMoMUq6AmSKCw/640?wx_fmt=png)

先试一下它的功能，传入两个索引类型：

![](https://mmbiz.qpic.cn/mmbiz_png/YprkEU0TtGiayopgibpqvyxjgLfUK65VLg4fQKfJyicGtnCBDcga05vmic4SXLfVeW92awj9xGiayib3GYKr6gCBbxPA/640?wx_fmt=png)

看下结果：

![](https://mmbiz.qpic.cn/mmbiz_png/YprkEU0TtGiayopgibpqvyxjgLfUK65VLgicQsaXic1CA2XCnKG06W8589j1ovYjPyohFe66e5EdVkADhFw9bJ762A/640?wx_fmt=png)

这是些啥啊，谁能看得懂呀。

其实这只是因为 TS 没有计算出最终的类型而已，用到的时候才会计算，所以我们可以这样处理下：

![](https://mmbiz.qpic.cn/mmbiz_png/YprkEU0TtGiayopgibpqvyxjgLfUK65VLgjZ84uSWmXHTDRlmNu4clLOotnFy4jpeY2AO56O9BnWibJyibdUW69iaLg/640?wx_fmt=png)

Copy 的高级类型是通过映射类型的语法构造了一个新的索引类型，它的功能是原封不动的复制一个索引类型。

类型参数 Obj 约束为索引类型，也就是 Record<string, any>。key 保持不变，也就是 Key in keyof Obj，value 也保持不变，也就是 Obj[Key]。

因为重新生成的类型的过程中要做计算，所以那个类型就能提示出最终的结果了：

![](https://mmbiz.qpic.cn/mmbiz_png/YprkEU0TtGiayopgibpqvyxjgLfUK65VLgWQr42m9ib9fbX3BooapHwG898KaglkQnDtS9BrFmO6ziaUCKfKnHLwTw/640?wx_fmt=png)

所以说，这个类型的作用是两个索引类型 A，B，只有 A 中有的就保留，A、B 都有的变为可选，B 有但 A 没有的变为可选。

那这段逻辑具体是怎么用 TS 实现的呢？

我们先来过一下 TS 这些内置的高级类型：

### Pick

Pick 的作用是通过映射类型的语法构造一个新的索引类型，根据传入的 Key 对索引做下过滤：

```
type Pick<T, K extends keyof T> = { [P in K]: T[P]; };
```

测试下：

![](https://mmbiz.qpic.cn/mmbiz_png/YprkEU0TtGiayopgibpqvyxjgLfUK65VLgA6IDNxNz28sLdVlibeuG7VWOP3SzCfV2ianuEsSn44ySZODkYQmxCNQw/640?wx_fmt=png)

### Partial

Partial 也是通过映射类型的语法构造一个新的索引类型，但是会把索引变为可选：

```
type Partial<T> = { [P in keyof T]?: T[P]; };
```

测试下：

![](https://mmbiz.qpic.cn/mmbiz_png/YprkEU0TtGiayopgibpqvyxjgLfUK65VLgOEuMkVTDMS0fiaibwE3SwxGCxDeoicHYdv196O6FJ0zKW2tHQJzxPgu4Q/640?wx_fmt=png)

### Extract

Extract 是取两个联合类型都包含的部分，也就是取交集：

```
type Extract<T, U> = T extends U ? T : never;
```

测试下：

![](https://mmbiz.qpic.cn/mmbiz_png/YprkEU0TtGiayopgibpqvyxjgLfUK65VLgB5KonrsTMzVPQE5KctHI006xlQjx3ico7yiahHRibtlc5IbxlWfAf5Kjw/640?wx_fmt=png)

### Exclude

Exclude 是从联合类型 A 中去掉联合类型 B 中的类型，也就是取差集：

```
type Extract<T, U> = T extends U ? T : never;
```

测试下：

![](https://mmbiz.qpic.cn/mmbiz_png/YprkEU0TtGiayopgibpqvyxjgLfUK65VLgB5KonrsTMzVPQE5KctHI006xlQjx3ico7yiahHRibtlc5IbxlWfAf5Kjw/640?wx_fmt=png)

学会了用 Pick、Partial、Exclude、Extract 这些高级类型，那上面的那段逻辑我们就知道怎么实现了：

只有 A 中有的就保留的逻辑是：Pick<A, Exclude<keyof A, keyof B>>

A、B 都有的变为可选：Partial<Pick<A, Extract<keyof A, keyof B>>>

B 中有但 A 中没有的也变为可选：Partial<Pick<B, Exclude<keyof B, keyof A>>>

这样，这个类型的主要逻辑我们就理清了：

![](https://mmbiz.qpic.cn/mmbiz_png/YprkEU0TtGiayopgibpqvyxjgLfUK65VLguHQ1HvJMDR7QkRaTiaryo54ic7PGRajDBYfCGJCSZSibs9SsCT82RRwXQ/640?wx_fmt=png)

把三部分计算结果的索引类型取交叉类型，就会合并到一起。

那前面那两个判断是啥？

P extends any 还有这个 string extends keyof P，这俩都是做啥的？

P extends any 这个是因为**联合类型当作为类型参数出现在条件类型左边时，会把每个类型单独传入做计算，最后把计算结果合并成联合类型，这个特性叫做分布式条件类型。**

比如这样一个联合类型：

```
type Union = 'a' | 'b' | 'c';
```

我们想把其中的 a 大写，就可以这样写：

```
type UppercaseA<Item extends string> =     Item extends 'a' ?  Uppercase<Item> : Item;
```

![](https://mmbiz.qpic.cn/mmbiz/YprkEU0TtGiayopgibpqvyxjgLfUK65VLgdrzibuutBtGeBEdLWCgNQ30zgznBuicqs1CF5JxnIsOsa8BcXHQjjOwg/640?wx_fmt=jpeg)

因为 Item 是类型参数，出现在了条件类型的左边，而且传入的是联合类型，那就触发分发特性，会把每个类型单独传入做计算，然后把结果合并成联合类型。

所以这里的 P extends any 的作用就是触发联合类型特性的，从而让这个类型能正确处理联合类型。不然联合类型整个传入的话，后面怎么做计算。

这里的 P extends any 换成 P extends P 也可以，都是一样的作用。

那后面那段代码 string extends keyof P 是啥意思？

这个我确实想了一段时间，如果 {a: 1, b: 2} 这样的索引类型，keyof 的结果是'a' | 'b'，而如果是数组类型，那 keyof 的结果是 0 | 1 | 'length' | 'toString' | ...

什么类型的 keyof 结果是 string 呢？

突然，我想起了前几天学到的一个知识点：用 keyof any 代替 string | number | symbol 更灵活：

![](https://mmbiz.qpic.cn/mmbiz_png/YprkEU0TtGiayopgibpqvyxjgLfUK65VLgV9b7mIpmhICFZz3CWPK8uMaoicb2Et8hHzaC6Gv1sXYMa1dOXtD1hew/640?wx_fmt=png)

而且我试了下 never 的 keyof 结果也是这个：

![](https://mmbiz.qpic.cn/mmbiz_png/YprkEU0TtGiayopgibpqvyxjgLfUK65VLgfshPRDYGXpPzegaAobW2IpX0iaicpmRCEaa0x9Y7z2ehzhJaFWOn35iag/640?wx_fmt=png)

所以说 string extends keyof P 就可以排除 any 和 never 的情况！

妙呀，还能这么区分 any 和 never。

所以说，这个类型的逻辑我们已经理清了：

![](https://mmbiz.qpic.cn/mmbiz_png/YprkEU0TtGiayopgibpqvyxjgLfUK65VLgBB3noPsDUKuOXuEFRibSonNsLL39hPibHlphFGwyQzEybGfKPLV1sPFQ/640?wx_fmt=png)

这个类型的功能是保留只有 A 有的索引，把 A、B 都有的索引变为可选，把只有 B 有的索引变为可选。

而且处理了联合类型的情况。

如果传入的是 any 或者 never，不做处理，直接返回。

这个类型里我们也学到了不少东西。

总结
--

我看了下 @types/react 的类型定义，学到了不少东西：

*   可选索引的值的提取，用 infer 比 Obj[key] 更方便，因为前者只需要 Obj[Key] extends { xxx?: infer Value| undefined} 提取 Value 的类型，而后者需要先排除值的类型就是 undefined 的情况，然后再用 Exclude<Obj[Key], undefined> 去掉类型中的 undefined。
    
*   ts 3.0 中取索引类型没有的索引会返回 {} 而不是 never，需要兼容的话可以单独做下判断：'xxx' in keyof Obj。
    
*   处理索引类型可以综合用 Pick、Partial、Exclude、Extract 等内置高级类型对每一部分索引做处理，然后取交叉类型来合并到一起。
    
*   P extends any 和 P extends P 的作用是触发联合类型的分发特性的，加上这段处理才能正确处理联合类型，会把每个类型单独传入做计算，最后把结果合并成联合类型。
    
*   string extends keyof Obj 可以判断出 any 和 never 类型，只有这两种类型取 keyof 的结果是 string | nubmer | symbole，包含 string。
    

而且，还讲了一个小技巧：

ts 类型只有计算的时候才会求值，如果是索引类型，可以用映射类型的语法创建个一摸一样的索引类型，因为用到了，就会做计算，从而就可以显示出最终的类型。

不得不说，React 类型定义做的挺完善的，考虑到了各种类型的处理，也考虑到了低版本的兼容，从中还是能学到不少东西的。