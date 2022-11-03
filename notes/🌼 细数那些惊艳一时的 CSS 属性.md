> 本文由 [简悦 SimpRead](http://ksria.com/simpread/) 转码， 原文地址 [juejin.cn](https://juejin.cn/post/7155780555554947102)

持续创作，加速成长！这是我参与「掘金日新计划 · 10 月更文挑战」的第 21 天，[点击查看活动详情](https://juejin.cn/post/7147654075599978532 "https://juejin.cn/post/7147654075599978532")

前言
==

随着前端的不断发展，更多新的 CSS 属性不断加入提案，本文列举 7 个不常用但很有用且你见过也可能没见过的 CSS 属性，带大家领略 CSS 之美。

1. position: sticky
===================

![](https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/4efbc0bc2b3b4586949076d1d4241ca8~tplv-k3u1fbpfcp-zoom-in-crop-mark:4536:0:0:0.image?)

不知道大家平时业务开发中有没有碰到像上图一样的吸顶的需求：标题在滚动的时候，会一直贴着最顶上。

这种场景实际上很多：比如表格的标题栏、网站的导航栏、手机通讯录的人名首字母标题等等。如果让大家自己动手做的话，是不是会用 js 结合 css 来实现呢？以前确实是这样的，直到后来 `position` 属性新增了一个属性值 `sticky` ，前端程序员才迎来了小春天。

```
// css 部分
.container {
    background-color: oldlace;
    height: 200px;
    width: 140px;
    overflow: auto;
  }
  .container div {
    height: 20px;
    background-color: aqua;
    border: 1px solid;
  }
  .container .header {
    position: sticky;
    top: 0;
    background-color: rgb(187, 153, 153);
}

// html 部分
<div class="container">
  <div class="header">Header</div>
  <div>1</div>
  <div>2</div>
  <div>3</div>
</div>
复制代码
```

就这么简单？对，就这么简单，但是小伙伴们使用的时候要注意兼容性。

2. :empty 选择器
=============

![](https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/496dcc35befd4372afa91212fbd7ed56~tplv-k3u1fbpfcp-zoom-in-crop-mark:4536:0:0:0.image?)

平时开发的时候数据都是通过请求接口获取的，也会存在接口没有数据的情况。这个时候正常的做法是给用户一个提示，让用户知道当前不是出 bug 了，而是确实没有数据。

一般的做法是我们人为的判断当前数据返回列表的长度是否为 0，如果为 0 则显示一个 “暂无数据” 给用户，反之则隐藏该提示。写过 Vue 的小伙伴是不是经常这么做：

```
<div>
    <template v-if="datas.length">
        <div v-for="data in datas"></div>
    </template>
    <template v-else>
        <div>暂无数据</div>
    </template>
</div>
复制代码
```

但是有了 `:empty` 这个选择器后，你大可以把这个活交给 CSS 来干。

```
.container {
    height: 400px;
    width: 600px;
    background-color: antiquewhite;
    display: flex;
    justify-content: center;
    align-items: center;
}
.container:empty::after {
    content: "暂无数据";
}
复制代码
```

通过 `:empty` 选中内容为空的容器，然后通过伪元素为空容器添加提示。是不是方便很多呢？

3. gap
======

小伙伴们日常开发中，都有用过 `padding` 和 `margin` 吧，`margin` 一般用做边距，让两个元素隔开一点距离，但是对于一些场景下，我们很难通过计算得到一个除的尽的值，比如 100px 我要让 3 个元素等分，且每个元素隔开 10px，这就很尴尬了。

没关系！我们可以用 `gap` 属性，`gap` 属性它适用于 Grid 布局、Flex 布局以及多列布局，并不一定只是 Grid 布局中可以使用。

比如我们要让每个元素之间隔开 20px， 那么使用 `gap` 我们可以这样：

```
display: flex | grid；
gap: 20px;
复制代码
```

是不是很简单呢？

4. background-clip: text
========================

这个属性可能小伙伴们用的不多，有什么用呢？简单来说就是可以做一个带背景的文字效果：

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/7b47d64801574758b08bc4639c4698d7~tplv-k3u1fbpfcp-zoom-in-crop-mark:4536:0:0:0.image?)

![](https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/250a7d3b58a844ce9875410ec3b262cb~tplv-k3u1fbpfcp-zoom-in-crop-mark:4536:0:0:0.image?)

![](https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/babec885491e46858864fabba3a84b0a~tplv-k3u1fbpfcp-zoom-in-crop-mark:4536:0:0:0.image?)

图 1 + 图 2 = 图 3。是不是很惊艳呢？大家平时 `background-clip` 是不是都用来做一些裁切效果？in 知道它还有个属性值是 `text` 吗？`background-clip: text` 用来做带背景的文字效果，相信大家平时浏览一些网站的时候都会看到类似的实现，实际上通过 CSS 我们也能做到这种效果，可不要傻傻的以为都是用制图工具做的。

5. user-select
==============

很多人不知道这个属性是什么意思。大家看看下图：

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/0c1d19d4885541769b4b2fe628631fe2~tplv-k3u1fbpfcp-zoom-in-crop-mark:4536:0:0:0.image?)

网页和 APP 有个不同点是，网页上的字是可以通过光标选中的，而 APP 的不行。

有的小伙伴可能会疑惑：那我网页上也用不着这个属性啊？

非也非也，我们知道现在很多新的技术产生，可以在 APP 上嵌套 **webview** 或者是**网页**，比如 **Electron** 做的 **桌面端应用** ，大家没见过哪个桌面端应用是可以光标选中的吧？

而 `user-select` 属性可以 **禁用光标选中** ，让网页看着和移动端一样。

6. :invalid 伪类
==============

**`:invalid`**  表示任意内容未通过验证的 `input` 或其他 `form` 元素。什么意思呢？举个例子。

这是一个表单。

```
<form>
  <label for="url_input">Enter a URL:</label>
  <input type="url" id="url_input" />
  <br />
  <br />
  <label for="email_input">Enter an email address:</label>
  <input type="email" id="email_input" required/>
</form>
复制代码
```

我们的需求是让 `input` 当值有效时，元素颜色为绿色，无效时为红色。

```
input:invalid {
  background-color: #ffdddd;
}

form:invalid {
  border: 5px solid #ffdddd;
}

input:valid {
  background-color: #ddffdd;
}

form:valid {
  border: 5px solid #ddffdd;
}

input:required {
  border-color: #800000;
  border-width: 3px;
}

input:required:invalid {
  border-color: #C00000;
}
复制代码
```

有了 `:invalid` 属性后，我们就可以不用 JS 也能实现校验提示的效果了。

7. :focus-within 伪类
===================

**`:focus-within`** 表示一个元素获得焦点，或该元素的后代元素获得焦点，就会匹配上。啥意思呢？

话不多说，先看图：

![](https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/155505a845f24d44b667dd4e3249e77f~tplv-k3u1fbpfcp-zoom-in-crop-mark:4536:0:0:0.image?)

```
// CSS
form {
    border: 1px solid;
    width: 400px;
    height: 300px;
    display: flex;
    justify-content: center;
    align-items: center;
}
form:focus-within {
    box-shadow: 0px 4px 4px rgba(0, 0, 0, 0.3);
    background-color: beige;
}

// HTML
<form>
  <input type="text" id="ipt" placeholder="请输入..." />
</form>
复制代码
```

可以根据子元素的状态来改变父元素的样式，方便的很。也能玩出不少花样来。

有的小伙伴看完后可能会想：嗷，也没啥的啊，这有啥花样？

给大家推荐一篇文章，看完就懂了：

[纯 css 实现：仿掘金账户密码登录时，小熊猫捂眼动作切换的小彩蛋 - 掘金 (juejin.cn)](https://juejin.cn/post/6970979674092634120 "https://juejin.cn/post/6970979674092634120")

8. mix-blend-mode:difference
============================

`mix-blend-mode:difference` 属性描述了元素的内容应该与元素的直系父元素的内容和元素的背景如何混合。其中，`difference` 表示差值。

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/f3848be4f3e54825ab25588e324e8701~tplv-k3u1fbpfcp-zoom-in-crop-mark:4536:0:0:0.image?)

如果给你这张图，你会怎么去绘制？是不是很麻烦。

但是如果用 `mix-blend-mode:difference` 这个属性，就可以很容易的实现。

```
.mode {
    display: flex;
    justify-content: center;
    align-items: center;
    mix-blend-mode:difference;
}
  .dark {
    position: relative;
    left: 6px;
    height: 24px;
    width: 24px;
    background-color: grey;
    border-radius: 50%;
}
.light {
    mix-blend-mode:difference;
    position: relative;
    left: -6px;
    height: 16px;
    width: 16px;
    border-radius: 50%;
    border: 4px solid grey;
}
复制代码
```

关于 `mix-blend-mode:difference` 的更多用法，我推荐大家一篇文章：

> [谈谈一些有趣的 CSS 题目（十七）-- 不可思议的颜色混合模式 mix-blend-mode - 掘金 (juejin.cn)](https://juejin.cn/post/6844903477429141517 "https://juejin.cn/post/6844903477429141517")

结束语
===

本文就到此结束了，希望大家共同努力，早日拿下 CSS 💪💪。

如果文中有不对的地方，或是大家有不同的见解，欢迎指出 🙏🙏。

如果大家觉得所有收获，欢迎一键三连💕💕。