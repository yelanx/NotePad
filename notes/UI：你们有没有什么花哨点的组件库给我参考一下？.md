> 本文由 [简悦 SimpRead](http://ksria.com/simpread/) 转码， 原文地址 [juejin.cn](https://juejin.cn/post/7013247812628381704)

⚠️ 本文为掘金社区首发签约文章，未获授权禁止转载

前言
==

在刚刚过去的国庆节我们公司上线了好几个国庆的活动页🎉，之前开会探讨这些活动页的时候`UI`小姐姐曾问到：“你们前端有没有那种比较炫酷的组件库啊？不是像咱们平时页面里的那种很普通的组件，有的话可以给我看看，我就按照组件库的风格出图，这样你省事我也省事，咱们进度也能更快一点。活动页就是要做的标新立异一些，但我要是太标新立异了怕你们做起来麻烦导致咱们不能在国庆前上线，所以你去你们程序员的 `GitHub`、`CSS Tricks`、`CodePen` 之类的网站上找找有没有这样的库吧！”

我：这当然有啦！你看看这个咋样：

![](https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/d61f66ca732140adba0d8b8ece7fdcac~tplv-k3u1fbpfcp-watermark.awebp?)

她：哟！这个效果还不错！我还以为你们前端的组件库都是`Element UI`、`Ant Design`那种样式的呢。

我：这你可就孤陋寡闻了吧，我们前端花里胡哨的组件库可多了去了，只不过是因为咱们这种以内容为导向的网站大多都用不到什么特别炫酷的效果吧？所以才很少会看到这种效果的组件库。

她：有道理，还有哪些组件啊？都给我看看，咱们活动页就需要这种标新立异的组件来点缀。

花式组件库
=====

组件库的名字叫 [fancy-components](https://link.juejin.cn?target=https%3A%2F%2Fwww.bilibili.com%2Fvideo%2FBV1Du411Z7Sc "https://www.bilibili.com/video/BV1Du411Z7Sc")，但我更喜欢把它翻译成中文：`花式组件库`。下面就来看看它都有哪些组件，对不对得起它这么嚣张的名字。

按钮类
---

### FcWarpBtn

![](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/e8c4515a6eea4bbe956bfcb138349335~tplv-k3u1fbpfcp-watermark.awebp?)

warp 是变形的意思，顾名思义，这个按钮看起来像是变了形翘起来了一样。

### FcDblWarpBtn

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/c76733471756457f8e9f860794f13c6e~tplv-k3u1fbpfcp-watermark.awebp?)

`Dbl`就是`double`的简写，上面那个按钮不是只有一边翘起来了么？那这个是两边都翘了，所以加了个`Dbl`。

### Fc3DBtn

![](https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/c05dde8de65e44ee83cfbfd3087b76bf~tplv-k3u1fbpfcp-watermark.awebp?)

顾名思义，3D 嘛！非常立体的这么一种感觉。

### FcUnderlineBtn

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/5043e055a83a444b8a5f06298b713dcd~tplv-k3u1fbpfcp-watermark.awebp?)

`Underline`下划线的意思，这款按钮在非`hover`状态时会有一个下划线。

### FcRoundBtn

![](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/6d98281794fb422eab7d5cec6ad2e7d2~tplv-k3u1fbpfcp-watermark.awebp?)

`Round`圆润的意思嘛！圆润的一款按钮。

### FcParenthesesBtn

![](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/8260096ead49486d8c497a460a2bc14b~tplv-k3u1fbpfcp-watermark.awebp?)

`Parentheses`是括号的意思，这款按钮在非`hover`状态时会有一个括号。

### FcPixelBtn

`Pixel`是像素的意思，这款按钮在`hover`时会有一个像素风的动画。

### FcArrowBtn

![](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/7d946fe4ddef458390391c6d1f696743~tplv-k3u1fbpfcp-watermark.awebp?)

`Arrow`是箭头的意思，箭头按钮非常适合作为返回按钮等指引按钮。

表单类
---

### FcTypingInput

![](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/e4b5ad7dc0784525bfbee949c76bcd1a~tplv-k3u1fbpfcp-watermark.awebp?)

![](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/6aa1552e466b41939b7d3d8abea52c6e~tplv-k3u1fbpfcp-watermark.awebp?)

`Typing`是打字的意思，这个输入框在`focus`状态时的`placeholder`会出现一种类似打字的效果。

滤镜类
---

### FcWaveFilter

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/a27a5b92b8c1464a9ec2bffce9aca51c~tplv-k3u1fbpfcp-watermark.awebp)

`Wave`是波浪的意思，它会把传进来的图片加以波浪效果，更具体的用法请[点击这里](https://juejin.cn/post/7005763586999975943 "https://juejin.cn/post/7005763586999975943")。

渐进增强类
-----

### FcBubbles

![](https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/d61f66ca732140adba0d8b8ece7fdcac~tplv-k3u1fbpfcp-watermark.awebp?)

`Bubbles`是气泡的意思，渐进增强式的组件就是你传进来的东西什么都不变，就是多加了个特效罢了。

![](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/fde6094a50664816b240d05a1cec5f0f~tplv-k3u1fbpfcp-watermark.awebp?)

国旗类
---

### FcChina

> （由于某些原因导致本文无法上传与🇨🇳有关的图片，具体的效果请点击[这个链接](https://link.juejin.cn?target=https%3A%2F%2Ffancy-components.github.io%2F%23%2F "https://fancy-components.github.io/#/")来查看）

`China`相信不用翻译大家也都知道是啥意思。

具体用法
====

首先这是一款基于 [web components](https://link.juejin.cn?target=https%3A%2F%2Fwww.bilibili.com%2Fvideo%2FBV1V341167LW%2F "https://www.bilibili.com/video/BV1V341167LW/") 的组件库，无论你是`vue`、`react`、`angular`还是`svelte`等一些其他框架，它都能良好的与这些框架共存，因为这就是`JS`原生的组件化技术。没有哪个框架说自己不支持原生`JS`的某一部分`API`的，这种技术有人看好也有人唱衰，看好的人觉得：

*   未来可能会取代框架 (这种扯淡的观点来自[外网一篇很火的文章](https://link.juejin.cn?target=https%3A%2F%2Fwww.dannymoerkerke.com%2Fblog%2Fweb-components-will-replace-your-frontend-framework "https://www.dannymoerkerke.com/blog/web-components-will-replace-your-frontend-framework"))
*   随着兼容性越来越好 主流框架将会用 [web components](https://link.juejin.cn?target=https%3A%2F%2Fwww.bilibili.com%2Fvideo%2FBV1V341167LW%2F "https://www.bilibili.com/video/BV1V341167LW/") 重写自己的组件化系统 (这种观点还稍微靠谱点)
*   由于良好的跨框架性 越来越多的组件库将会采用 [web components](https://link.juejin.cn?target=https%3A%2F%2Fwww.bilibili.com%2Fvideo%2FBV1V341167LW%2F "https://www.bilibili.com/video/BV1V341167LW/") 来写 ([fancy-components](https://link.juejin.cn?target=https%3A%2F%2Fwww.bilibili.com%2Fvideo%2FBV1Du411Z7Sc "https://www.bilibili.com/video/BV1Du411Z7Sc") 就是很好的一个案例)

除了 [fancy-components](https://link.juejin.cn?target=https%3A%2F%2Fwww.bilibili.com%2Fvideo%2FBV1Du411Z7Sc "https://www.bilibili.com/video/BV1Du411Z7Sc")，我还经常用的一个 [web components](https://link.juejin.cn?target=https%3A%2F%2Fwww.bilibili.com%2Fvideo%2FBV1V341167LW%2F "https://www.bilibili.com/video/BV1V341167LW/") 组件是 [css doodle](https://link.juejin.cn?target=https%3A%2F%2Fwww.bilibili.com%2Fvideo%2FBV19v411P7oC "https://www.bilibili.com/video/BV19v411P7oC")，翻译过来是 [CSS 涂鸦](https://link.juejin.cn?target=https%3A%2F%2Fwww.bilibili.com%2Fvideo%2FBV19v411P7oC "https://www.bilibili.com/video/BV19v411P7oC")的意思。它不能算是一个组件库，因为它只有一个组件，但是可以通过这个组件画出万花筒一样的样式来，也是超炫的一个 [web components](https://link.juejin.cn?target=https%3A%2F%2Fwww.bilibili.com%2Fvideo%2FBV1V341167LW%2F "https://www.bilibili.com/video/BV1V341167LW/") 组件：

![](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/4c7045b9e31a4f2c8949d98e0f6f2223~tplv-k3u1fbpfcp-watermark.awebp)

感兴趣的同学可以去`B站搜索`：[前端学不动](https://link.juejin.cn?target=https%3A%2F%2Fspace.bilibili.com%2F1076738791 "https://space.bilibili.com/1076738791")这位 up 主，看看上面那个效果是怎么实现出来的。

现如今已经有很多组件库都采用了 [web components](https://link.juejin.cn?target=https%3A%2F%2Fwww.bilibili.com%2Fvideo%2FBV1V341167LW%2F "https://www.bilibili.com/video/BV1V341167LW/") 作为底层技术，已经可以看到这种趋势了。只不过 [web components](https://link.juejin.cn?target=https%3A%2F%2Fwww.bilibili.com%2Fvideo%2FBV1V341167LW%2F "https://www.bilibili.com/video/BV1V341167LW/") 在国内很少被人谈起，所以那些在国外还不错的一些组件库也就没能传到大家的耳朵里。

就连新发布的`Vue3.2`也出了一个 [defineCustomElement](https://link.juejin.cn?target=https%3A%2F%2Fv3.cn.vuejs.org%2Fapi%2Fglobal-api.html%23definecustomelement "https://v3.cn.vuejs.org/api/global-api.html#definecustomelement") 方法，就是为了专门用来生成[自定义元素 (web components 的其中一项技术)](https://link.juejin.cn?target=https%3A%2F%2Fwww.bilibili.com%2Fvideo%2FBV1EA411A7FW "https://www.bilibili.com/video/BV1EA411A7FW") 的。目前已经有趋势表明这门技术未来将会大展身手，以前它没能火起来的主要原因就是兼容性问题。

不过既然有人看好那就肯定有人唱衰，唱衰的人觉得：

*   几乎所有流行框架都拥有自己的一套组件化系统 何必大费周折的去用 [web components](https://link.juejin.cn?target=https%3A%2F%2Fwww.bilibili.com%2Fvideo%2FBV1V341167LW%2F "https://www.bilibili.com/video/BV1V341167LW/") 重构呢？
*   这么多年了还没火起来 不是没给它时间 是它自己不争气啊！
*   不兼容`IE`的技术不是好技术

为何说 [web components](https://link.juejin.cn?target=https%3A%2F%2Fwww.bilibili.com%2Fvideo%2FBV1V341167LW%2F "https://www.bilibili.com/video/BV1V341167LW/") 未来可能会取代框架这个观点有点扯淡呢？发布这个观点的作者认为`jQuery`的没落源于浏览器实现了`document.querySelector`和`document.querySelectorAll`方法，这不就跟`jQuery`的主要方法`$()`有些相似么，那谁还用`jq`。所以`web components`也是同理：现代框架的主要卖点不就是`组件化`么？浏览器原生实现了组件化功能，所以现代框架未来也会像`jq`那样。（_个人感觉纯属扯淡_）

不过毕竟咱们这篇文章不是讲 [web components](https://link.juejin.cn?target=https%3A%2F%2Fwww.bilibili.com%2Fvideo%2FBV1V341167LW%2F "https://www.bilibili.com/video/BV1V341167LW/") 的，只不过是咱们用到的组件库 [fancy-components](https://link.juejin.cn?target=https%3A%2F%2Fwww.bilibili.com%2Fvideo%2FBV1Du411Z7Sc "https://www.bilibili.com/video/BV1Du411Z7Sc") 是用 [web components](https://link.juejin.cn?target=https%3A%2F%2Fwww.bilibili.com%2Fvideo%2FBV1V341167LW%2F "https://www.bilibili.com/video/BV1V341167LW/") 来进行封装的，就简单的提一嘴 [web components](https://link.juejin.cn?target=https%3A%2F%2Fwww.bilibili.com%2Fvideo%2FBV1V341167LW%2F "https://www.bilibili.com/video/BV1V341167LW/")。咱们只需要知道怎么用 [fancy-components](https://link.juejin.cn?target=https%3A%2F%2Fwww.bilibili.com%2Fvideo%2FBV1Du411Z7Sc "https://www.bilibili.com/video/BV1Du411Z7Sc") 就行：

在普通项目中
------

普通项目指的是那种直接在文件夹里建个`HTML`文件就开始写代码的那种项目，没有什么工程化、webpack、babel 这些花里胡哨的东西。我们新建个`HTML`然后在`<script>`标签中写上`type="module"`就能使用模块化的语法来加载 [fancy-components](https://link.juejin.cn?target=https%3A%2F%2Fwww.bilibili.com%2Fvideo%2FBV1Du411Z7Sc "https://www.bilibili.com/video/BV1Du411Z7Sc") 啦：

```
<!DOCTYPE html>
<html>
<head>
  <meta charset="UTF-8">
  <title>公众号：前端学不动</title>
  <style>
    * {
      padding: 0;
      margin: 0;
    }
    html, body {
      height: 100%;
      display: grid;
      place-items: center;
    }
    fc-wave-filter {
      --width: 100px;
    }

    /* :defined 选择器会选择到已定义的元素 */
    /* 但却没有 :undefined 这样的选择器 所以只能用 :not() 选择器来配合使用 */
    /* 这个选择器具体是干嘛用的 可以B站搜索：前端学不动 这位up主 */
    /* 找到他的《自定义元素基础篇》在第 17:25 的位置有讲 */
    :not(:defined) {
      display: none;
    }
  </style>
</head>
<body>
  <!-- 需要在<fc-wave-filter>里指定color这个属性 默认为黑色 -->
  <fc-wave-filter color="#047">
    <!-- 在这里(slot里)放入img图片 -->
    <img src="data:image/svg+xml;base64,PHN2ZyBjbGFzcz0iaWNvbiIgc3R5bGU9IndpZHRoOjFlbTtoZWlnaHQ6MWVtO3ZlcnRpY2FsLWFsaWduOm1pZGRsZSIgdmlld0JveD0iMCAwIDEwMjQgMTAyNCIgeG1sbnM9Imh0dHA6Ly93d3cudzMub3JnLzIwMDAvc3ZnIiBmaWxsPSJjdXJyZW50Q29sb3IiIG92ZXJmbG93PSJoaWRkZW4iPjxwYXRoIGQ9Ik0yNjQuMTI5IDI2MS4yNjJjNS4wMjUtNS45NCA0LjI4NS0xNS4yNS0zLjAwOS0yMS40MS02LjQ3NS01LjQzNS0xNi4wODUtMy45MzgtMjAuMzU0IDEuMTk4bC0zOC4wNzcgNDUuMzIzIDIzLjY2MiAxOS44NjYgMzcuNzc4LTQ0Ljk3N3ptLTQzLjkzNy01MC4wMTljLTYuMTQ0LTUuMTA0LTE1LjU2NS01LjI3Ny0xOS43ODctLjMxNWwtMzYuMjAzIDQzLjE2NiAyMi40MDIgMTguNzk0IDM1Ljc5My00Mi42NjFjNC41MzctNS4zNTcgNC41MDYtMTMuMzQ0LTIuMjA1LTE4Ljk4NHptNTY2LjQxMyAxMzUuODE0bC0yMi40NDktMjcuOTE2IDQ0LjcyNS02NS4zNzgtLjI4My0uMzE1LTczLjIwOCAzMC4xMjEtMjIuNjg2LTI3Ljc1OCA3Ny40OTMtOTkuNzIyIDE5LjEyNSAyMy42My00OC41MjIgNjMuNTA1IDc0LjE4NS0zMS44NyAyMC41MTIgMjUuMzYzLTQ2LjU2OSA2NS45MyA3Mi4yLTM0LjI4IDE5LjEyNiAyMy41OTktMTEzLjY0OSA1NS4wOTF6TTUwNy4xNjQgODA3LjAzOGMtMTY1LjYwNSAwLTI5OS44NzUtMTM0LjI1NC0yOTkuODc1LTI5OS44NzQgMC0xNjUuNTczIDEzNC4yNy0yOTkuODQzIDI5OS44NzUtMjk5Ljg0MyAxNjUuNjIgMCAyOTkuODc0IDEzNC4yNTQgMjk5Ljg3NCAyOTkuODQzIDAgMTY1LjYyLTEzNC4yNTQgMjk5Ljg3NC0yOTkuODc0IDI5OS44NzR6TTEyNi45MjkgMjY0Ljk4bDYwLjI5LTcxLjgyMmMxMi41MDgtMTQuODg3IDI5Ljk2NC0yNi43ODIgNDYuNTUyLTEyLjg0IDUuMTA1IDQuMjcgNy4yOTQgNS42ODggMTMuNTAxIDE3LjA0NiAxLjQ2NSA0LjAwMiAyLjU4NCAxMS4yOTYtLjE4OSAxOC40NDggMTQuNDE1LTguOTY0IDI5Ljc5LTYuMDM0IDM5LjcxNiAzLjYyMyAxNS4yODEgMTQuODU2IDEzLjA2IDMxLjk5Ni0xLjQ2NSA0OS4zMjZsLTYzLjU4MyA3NS43NzYtOTQuODIyLTc5LjU1N3ptMzExLjYxLTIwMy4yNTZoMzcuMjI3bDMzLjYxOSA4OCAzMy42MTktODhoMzcuMTYzdjEyMy41NzNoLTI0LjczNHYtODYuM2wtMzIuNTQ3IDg2LjNoLTI3LjA1bC0zMi40ODQtODYuM3Y4Ni4zSDQzOC41NFY2MS43MjR6bTY4LjYyNS0yMy45OTRDMjQ3LjkxOCAzNy43MyAzNy43MyAyNDcuOTE4IDM3LjczIDUwNy4xNjRjMCAyNTkuMjc2IDIxMC4xODggNDY5LjQ4IDQ2OS40MzQgNDY5LjQ4IDI1OS4zMDggMCA0NjkuNDgtMjEwLjIwNCA0NjkuNDgtNDY5LjQ2NVM3NjYuNDcyIDM3LjcxNSA1MDcuMTY0IDM3LjcxNXptMCA3NDkuOTk0YzE1NC45ODYgMCAyODAuNjA3LTEyNS41OSAyODAuNjA3LTI4MC41Nkg1MDcuMTY0Vjc4Ny43NHptLTI4MC41NzYtMjgwLjU2aDI4MC41NzZWMjI2LjYxOWMtMTU0Ljk3MSAwLTI4MC41NzYgMTI1LjYwNS0yODAuNTc2IDI4MC41NDV6bTI4MC41NzYgNDg5LjYzYy0yNjkuOTU4IDAtNDg5LjY0Ni0yMTkuNjU3LTQ4OS42NDYtNDg5LjYzIDAtMjY5Ljk1OCAyMTkuNjg4LTQ4OS42MyA0ODkuNjQ2LTQ4OS42MyAyNzAuMDA1IDAgNDg5LjY0NSAyMTkuNjcyIDQ4OS42NDUgNDg5LjYzIDAgMjY5Ljk3My0yMTkuNjQgNDg5LjYzLTQ4OS42NDUgNDg5LjYzem0wLTk5Ni43OTRDMjI3LjUgMCAwIDIyNy41MDEgMCA1MDcuMTY0YzAgMjc5LjY2MiAyMjcuNTAxIDUwNy4xOTUgNTA3LjE2NCA1MDcuMTk1IDI3OS42OTMgMCA1MDcuMTc5LTIyNy41MzMgNTA3LjE3OS01MDcuMTk1QzEwMTQuMzQzIDIyNy41IDc4Ni44NTcgMCA1MDcuMTYzIDB6IiBmaWxsPSIjQjlCRUMzIi8+PC9zdmc+" />
  </fc-wave-filter>

  <script type="module">
    // 从 fancy-components 中引入组件
    // 这里拿 FcWaveFilter 来举例
    import { FcWaveFilter } from 'https://unpkg.com/fancy-components'

    // new 就相当于全局注册了这个组件，相当于 Vue 的 Vue.component('fc-wave-filter', FcWaveFilter)
    new FcWaveFilter()
  </script>
</body>
</html>
复制代码
```

所用图片： ![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/6bf8ca10e73f4c62b6c21078df90eeea~tplv-k3u1fbpfcp-watermark.awebp)

在 [fancy-components](https://link.juejin.cn?target=https%3A%2F%2Fwww.bilibili.com%2Fvideo%2FBV1Du411Z7Sc "https://www.bilibili.com/video/BV1Du411Z7Sc") 中有个叫`FcWaveFilter`的组件，翻译过来就是`波浪滤镜`，也很形象，用这个组件把`<img>`标签包裹一下就可以变成这样： ![](https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/48ada661782b432595893c6a422c73e3~tplv-k3u1fbpfcp-watermark.awebp)

在 React 项目中
-----------

> `npm i fancy-components`

先在主文件中引入你想要的组件，然后 new 一下：

```
/* eslint-disable no-new */
import React from 'react'
import ReactDom from 'react-dom'
...
// 从 fancy-components 中引入 FcWaveFilter（波浪滤镜）
import { FcWaveFilter } from 'fancy-components'

// new 就相当于全局注册了这个组件，相当于 Vue 的 Vue.component('fc-wave-filter', FcWaveFilter)
new FcWaveFilter()
复制代码
```

然后在用的时候 (_xxx.jsx_)：

```
export default () => (
  <fc-wave-filter color="#047">
    <img src="data:image/svg+xml;base64,PHN2ZyBjbGFzcz0iaWNvbiIgc3R5bGU9IndpZHRoOjFlbTtoZWlnaHQ6MWVtO3ZlcnRpY2FsLWFsaWduOm1pZGRsZSIgdmlld0JveD0iMCAwIDEwMjQgMTAyNCIgeG1sbnM9Imh0dHA6Ly93d3cudzMub3JnLzIwMDAvc3ZnIiBmaWxsPSJjdXJyZW50Q29sb3IiIG92ZXJmbG93PSJoaWRkZW4iPjxwYXRoIGQ9Ik0yNjQuMTI5IDI2MS4yNjJjNS4wMjUtNS45NCA0LjI4NS0xNS4yNS0zLjAwOS0yMS40MS02LjQ3NS01LjQzNS0xNi4wODUtMy45MzgtMjAuMzU0IDEuMTk4bC0zOC4wNzcgNDUuMzIzIDIzLjY2MiAxOS44NjYgMzcuNzc4LTQ0Ljk3N3ptLTQzLjkzNy01MC4wMTljLTYuMTQ0LTUuMTA0LTE1LjU2NS01LjI3Ny0xOS43ODctLjMxNWwtMzYuMjAzIDQzLjE2NiAyMi40MDIgMTguNzk0IDM1Ljc5My00Mi42NjFjNC41MzctNS4zNTcgNC41MDYtMTMuMzQ0LTIuMjA1LTE4Ljk4NHptNTY2LjQxMyAxMzUuODE0bC0yMi40NDktMjcuOTE2IDQ0LjcyNS02NS4zNzgtLjI4My0uMzE1LTczLjIwOCAzMC4xMjEtMjIuNjg2LTI3Ljc1OCA3Ny40OTMtOTkuNzIyIDE5LjEyNSAyMy42My00OC41MjIgNjMuNTA1IDc0LjE4NS0zMS44NyAyMC41MTIgMjUuMzYzLTQ2LjU2OSA2NS45MyA3Mi4yLTM0LjI4IDE5LjEyNiAyMy41OTktMTEzLjY0OSA1NS4wOTF6TTUwNy4xNjQgODA3LjAzOGMtMTY1LjYwNSAwLTI5OS44NzUtMTM0LjI1NC0yOTkuODc1LTI5OS44NzQgMC0xNjUuNTczIDEzNC4yNy0yOTkuODQzIDI5OS44NzUtMjk5Ljg0MyAxNjUuNjIgMCAyOTkuODc0IDEzNC4yNTQgMjk5Ljg3NCAyOTkuODQzIDAgMTY1LjYyLTEzNC4yNTQgMjk5Ljg3NC0yOTkuODc0IDI5OS44NzR6TTEyNi45MjkgMjY0Ljk4bDYwLjI5LTcxLjgyMmMxMi41MDgtMTQuODg3IDI5Ljk2NC0yNi43ODIgNDYuNTUyLTEyLjg0IDUuMTA1IDQuMjcgNy4yOTQgNS42ODggMTMuNTAxIDE3LjA0NiAxLjQ2NSA0LjAwMiAyLjU4NCAxMS4yOTYtLjE4OSAxOC40NDggMTQuNDE1LTguOTY0IDI5Ljc5LTYuMDM0IDM5LjcxNiAzLjYyMyAxNS4yODEgMTQuODU2IDEzLjA2IDMxLjk5Ni0xLjQ2NSA0OS4zMjZsLTYzLjU4MyA3NS43NzYtOTQuODIyLTc5LjU1N3ptMzExLjYxLTIwMy4yNTZoMzcuMjI3bDMzLjYxOSA4OCAzMy42MTktODhoMzcuMTYzdjEyMy41NzNoLTI0LjczNHYtODYuM2wtMzIuNTQ3IDg2LjNoLTI3LjA1bC0zMi40ODQtODYuM3Y4Ni4zSDQzOC41NFY2MS43MjR6bTY4LjYyNS0yMy45OTRDMjQ3LjkxOCAzNy43MyAzNy43MyAyNDcuOTE4IDM3LjczIDUwNy4xNjRjMCAyNTkuMjc2IDIxMC4xODggNDY5LjQ4IDQ2OS40MzQgNDY5LjQ4IDI1OS4zMDggMCA0NjkuNDgtMjEwLjIwNCA0NjkuNDgtNDY5LjQ2NVM3NjYuNDcyIDM3LjcxNSA1MDcuMTY0IDM3LjcxNXptMCA3NDkuOTk0YzE1NC45ODYgMCAyODAuNjA3LTEyNS41OSAyODAuNjA3LTI4MC41Nkg1MDcuMTY0Vjc4Ny43NHptLTI4MC41NzYtMjgwLjU2aDI4MC41NzZWMjI2LjYxOWMtMTU0Ljk3MSAwLTI4MC41NzYgMTI1LjYwNS0yODAuNTc2IDI4MC41NDV6bTI4MC41NzYgNDg5LjYzYy0yNjkuOTU4IDAtNDg5LjY0Ni0yMTkuNjU3LTQ4OS42NDYtNDg5LjYzIDAtMjY5Ljk1OCAyMTkuNjg4LTQ4OS42MyA0ODkuNjQ2LTQ4OS42MyAyNzAuMDA1IDAgNDg5LjY0NSAyMTkuNjcyIDQ4OS42NDUgNDg5LjYzIDAgMjY5Ljk3My0yMTkuNjQgNDg5LjYzLTQ4OS42NDUgNDg5LjYzem0wLTk5Ni43OTRDMjI3LjUgMCAwIDIyNy41MDEgMCA1MDcuMTY0YzAgMjc5LjY2MiAyMjcuNTAxIDUwNy4xOTUgNTA3LjE2NCA1MDcuMTk1IDI3OS42OTMgMCA1MDcuMTc5LTIyNy41MzMgNTA3LjE3OS01MDcuMTk1QzEwMTQuMzQzIDIyNy41IDc4Ni44NTcgMCA1MDcuMTYzIDB6IiBmaWxsPSIjQjlCRUMzIi8+PC9zdmc+" />
  </fc-wave-filter>
)
复制代码
```

注意千万不要写成驼峰形式的标签：`<FcWaveFilter>`

因为写组件写习惯了，想用组件的时候自然而然的就会想到这种写法。但实际上`<fc-wave-filter>`就和那些`<div>`、`<a>`、`<p>`、`<ul>`、`<li>`标签一样，属于`HTML`的原生标签啦！毕竟是用浏览器原生的组件化系统 ([web components](https://link.juejin.cn?target=https%3A%2F%2Fwww.bilibili.com%2Fvideo%2FBV1V341167LW%2F "https://www.bilibili.com/video/BV1V341167LW/")) 实现的，而不是采用了`React`的组件化系统，所以表现形式还是不一样的，不要搞混咯！而且也不需要像以前那样在用之前要引入一下：

```
// 不需要写成这样：
import { FcWaveFilter } from 'fancy-components'

export default () => (
  <fc-wave-filter color="#047">
    <img src="data:image/svg+xml;base64,PHN2ZyBjbGFzcz0iaWNvbiIgc3R5bGU9IndpZHRoOjFlbTtoZWlnaHQ6MWVtO3ZlcnRpY2FsLWFsaWduOm1pZGRsZSIgdmlld0JveD0iMCAwIDEwMjQgMTAyNCIgeG1sbnM9Imh0dHA6Ly93d3cudzMub3JnLzIwMDAvc3ZnIiBmaWxsPSJjdXJyZW50Q29sb3IiIG92ZXJmbG93PSJoaWRkZW4iPjxwYXRoIGQ9Ik0yNjQuMTI5IDI2MS4yNjJjNS4wMjUtNS45NCA0LjI4NS0xNS4yNS0zLjAwOS0yMS40MS02LjQ3NS01LjQzNS0xNi4wODUtMy45MzgtMjAuMzU0IDEuMTk4bC0zOC4wNzcgNDUuMzIzIDIzLjY2MiAxOS44NjYgMzcuNzc4LTQ0Ljk3N3ptLTQzLjkzNy01MC4wMTljLTYuMTQ0LTUuMTA0LTE1LjU2NS01LjI3Ny0xOS43ODctLjMxNWwtMzYuMjAzIDQzLjE2NiAyMi40MDIgMTguNzk0IDM1Ljc5My00Mi42NjFjNC41MzctNS4zNTcgNC41MDYtMTMuMzQ0LTIuMjA1LTE4Ljk4NHptNTY2LjQxMyAxMzUuODE0bC0yMi40NDktMjcuOTE2IDQ0LjcyNS02NS4zNzgtLjI4My0uMzE1LTczLjIwOCAzMC4xMjEtMjIuNjg2LTI3Ljc1OCA3Ny40OTMtOTkuNzIyIDE5LjEyNSAyMy42My00OC41MjIgNjMuNTA1IDc0LjE4NS0zMS44NyAyMC41MTIgMjUuMzYzLTQ2LjU2OSA2NS45MyA3Mi4yLTM0LjI4IDE5LjEyNiAyMy41OTktMTEzLjY0OSA1NS4wOTF6TTUwNy4xNjQgODA3LjAzOGMtMTY1LjYwNSAwLTI5OS44NzUtMTM0LjI1NC0yOTkuODc1LTI5OS44NzQgMC0xNjUuNTczIDEzNC4yNy0yOTkuODQzIDI5OS44NzUtMjk5Ljg0MyAxNjUuNjIgMCAyOTkuODc0IDEzNC4yNTQgMjk5Ljg3NCAyOTkuODQzIDAgMTY1LjYyLTEzNC4yNTQgMjk5Ljg3NC0yOTkuODc0IDI5OS44NzR6TTEyNi45MjkgMjY0Ljk4bDYwLjI5LTcxLjgyMmMxMi41MDgtMTQuODg3IDI5Ljk2NC0yNi43ODIgNDYuNTUyLTEyLjg0IDUuMTA1IDQuMjcgNy4yOTQgNS42ODggMTMuNTAxIDE3LjA0NiAxLjQ2NSA0LjAwMiAyLjU4NCAxMS4yOTYtLjE4OSAxOC40NDggMTQuNDE1LTguOTY0IDI5Ljc5LTYuMDM0IDM5LjcxNiAzLjYyMyAxNS4yODEgMTQuODU2IDEzLjA2IDMxLjk5Ni0xLjQ2NSA0OS4zMjZsLTYzLjU4MyA3NS43NzYtOTQuODIyLTc5LjU1N3ptMzExLjYxLTIwMy4yNTZoMzcuMjI3bDMzLjYxOSA4OCAzMy42MTktODhoMzcuMTYzdjEyMy41NzNoLTI0LjczNHYtODYuM2wtMzIuNTQ3IDg2LjNoLTI3LjA1bC0zMi40ODQtODYuM3Y4Ni4zSDQzOC41NFY2MS43MjR6bTY4LjYyNS0yMy45OTRDMjQ3LjkxOCAzNy43MyAzNy43MyAyNDcuOTE4IDM3LjczIDUwNy4xNjRjMCAyNTkuMjc2IDIxMC4xODggNDY5LjQ4IDQ2OS40MzQgNDY5LjQ4IDI1OS4zMDggMCA0NjkuNDgtMjEwLjIwNCA0NjkuNDgtNDY5LjQ2NVM3NjYuNDcyIDM3LjcxNSA1MDcuMTY0IDM3LjcxNXptMCA3NDkuOTk0YzE1NC45ODYgMCAyODAuNjA3LTEyNS41OSAyODAuNjA3LTI4MC41Nkg1MDcuMTY0Vjc4Ny43NHptLTI4MC41NzYtMjgwLjU2aDI4MC41NzZWMjI2LjYxOWMtMTU0Ljk3MSAwLTI4MC41NzYgMTI1LjYwNS0yODAuNTc2IDI4MC41NDV6bTI4MC41NzYgNDg5LjYzYy0yNjkuOTU4IDAtNDg5LjY0Ni0yMTkuNjU3LTQ4OS42NDYtNDg5LjYzIDAtMjY5Ljk1OCAyMTkuNjg4LTQ4OS42MyA0ODkuNjQ2LTQ4OS42MyAyNzAuMDA1IDAgNDg5LjY0NSAyMTkuNjcyIDQ4OS42NDUgNDg5LjYzIDAgMjY5Ljk3My0yMTkuNjQgNDg5LjYzLTQ4OS42NDUgNDg5LjYzem0wLTk5Ni43OTRDMjI3LjUgMCAwIDIyNy41MDEgMCA1MDcuMTY0YzAgMjc5LjY2MiAyMjcuNTAxIDUwNy4xOTUgNTA3LjE2NCA1MDcuMTk1IDI3OS42OTMgMCA1MDcuMTc5LTIyNy41MzMgNTA3LjE3OS01MDcuMTk1QzEwMTQuMzQzIDIyNy41IDc4Ni44NTcgMCA1MDcuMTYzIDB6IiBmaWxsPSIjQjlCRUMzIi8+PC9zdmc+" />
  </fc-wave-filter>
)
复制代码
```

只要我们在主文件中引入并且`new`过就行：

```
import { FcWaveFilter } from 'fancy-components'

/* eslint-disable no-new */
new FcWaveFilter()
复制代码
```

在 Vue2 中
--------

在`React`中很好的一点是组件和原生标签写法不一样，很容易区分：

```
import Ul form 'xxx'

export default () => (
  <Ul>
    <li/>
  </Ul>
)
复制代码
```

大写的驼峰形式的标签就是组件，小写的标签就是`HTML`标签，这样相对容易区分。

但`Vue`就不一样了，`Vue`组件既可以写成大写字母开头的驼峰形式，又可以写成小写字母开头的短横线形式。那么问题来了：当我们写了一个短横线连接的组件时，它怎么知道我们写的到底是组件还是标签呢？

它的做法是把所有`HTML`以及`SVG`标签全列举出来，只要你写的标签不在这个范围内，那么就默认你是组件了，如果你还没有在`components`里注册过，那么就会在控制台打印一个警告。所以我们要告诉`Vue`：对我这个既不在`HTML`标签范围内，又不在`SVG`标签范围内，同时还不是`Vue`组件的标签来说就不要给我报警告啦，我确定一定以及肯定这不是个错误！

> `npm i fancy-components`

还是先来到主文件，引入`fancy-components`：

```
import Vue from 'vue'
import App from './App'
import { FcWaveFilter } from 'fancy-components'

/* eslint-disable no-new */
new FcWaveFilter()

new Vue({
  render: h => h(App)
}).$mount('#app')
复制代码
```

然后用`Vue.config`来配置需要忽略哪些标签：

```
import Vue from 'vue'
import App from './App'
import { FcWaveFilter } from 'fancy-components'

/* eslint-disable no-new */
new FcWaveFilter()

// 配置忽略以 fc- 作为开头的元素
Vue.config.ignoredElements = [/^fc-/]

new Vue({
  render: h => h(App)
}).$mount('#app')
复制代码
```

在用的时候 (_xxx.vue_)：

```
<template>
  <fc-wave-filter color="#047">
    <img src="data:image/svg+xml;base64,PHN2ZyBjbGFzcz0iaWNvbiIgc3R5bGU9IndpZHRoOjFlbTtoZWlnaHQ6MWVtO3ZlcnRpY2FsLWFsaWduOm1pZGRsZSIgdmlld0JveD0iMCAwIDEwMjQgMTAyNCIgeG1sbnM9Imh0dHA6Ly93d3cudzMub3JnLzIwMDAvc3ZnIiBmaWxsPSJjdXJyZW50Q29sb3IiIG92ZXJmbG93PSJoaWRkZW4iPjxwYXRoIGQ9Ik0yNjQuMTI5IDI2MS4yNjJjNS4wMjUtNS45NCA0LjI4NS0xNS4yNS0zLjAwOS0yMS40MS02LjQ3NS01LjQzNS0xNi4wODUtMy45MzgtMjAuMzU0IDEuMTk4bC0zOC4wNzcgNDUuMzIzIDIzLjY2MiAxOS44NjYgMzcuNzc4LTQ0Ljk3N3ptLTQzLjkzNy01MC4wMTljLTYuMTQ0LTUuMTA0LTE1LjU2NS01LjI3Ny0xOS43ODctLjMxNWwtMzYuMjAzIDQzLjE2NiAyMi40MDIgMTguNzk0IDM1Ljc5My00Mi42NjFjNC41MzctNS4zNTcgNC41MDYtMTMuMzQ0LTIuMjA1LTE4Ljk4NHptNTY2LjQxMyAxMzUuODE0bC0yMi40NDktMjcuOTE2IDQ0LjcyNS02NS4zNzgtLjI4My0uMzE1LTczLjIwOCAzMC4xMjEtMjIuNjg2LTI3Ljc1OCA3Ny40OTMtOTkuNzIyIDE5LjEyNSAyMy42My00OC41MjIgNjMuNTA1IDc0LjE4NS0zMS44NyAyMC41MTIgMjUuMzYzLTQ2LjU2OSA2NS45MyA3Mi4yLTM0LjI4IDE5LjEyNiAyMy41OTktMTEzLjY0OSA1NS4wOTF6TTUwNy4xNjQgODA3LjAzOGMtMTY1LjYwNSAwLTI5OS44NzUtMTM0LjI1NC0yOTkuODc1LTI5OS44NzQgMC0xNjUuNTczIDEzNC4yNy0yOTkuODQzIDI5OS44NzUtMjk5Ljg0MyAxNjUuNjIgMCAyOTkuODc0IDEzNC4yNTQgMjk5Ljg3NCAyOTkuODQzIDAgMTY1LjYyLTEzNC4yNTQgMjk5Ljg3NC0yOTkuODc0IDI5OS44NzR6TTEyNi45MjkgMjY0Ljk4bDYwLjI5LTcxLjgyMmMxMi41MDgtMTQuODg3IDI5Ljk2NC0yNi43ODIgNDYuNTUyLTEyLjg0IDUuMTA1IDQuMjcgNy4yOTQgNS42ODggMTMuNTAxIDE3LjA0NiAxLjQ2NSA0LjAwMiAyLjU4NCAxMS4yOTYtLjE4OSAxOC40NDggMTQuNDE1LTguOTY0IDI5Ljc5LTYuMDM0IDM5LjcxNiAzLjYyMyAxNS4yODEgMTQuODU2IDEzLjA2IDMxLjk5Ni0xLjQ2NSA0OS4zMjZsLTYzLjU4MyA3NS43NzYtOTQuODIyLTc5LjU1N3ptMzExLjYxLTIwMy4yNTZoMzcuMjI3bDMzLjYxOSA4OCAzMy42MTktODhoMzcuMTYzdjEyMy41NzNoLTI0LjczNHYtODYuM2wtMzIuNTQ3IDg2LjNoLTI3LjA1bC0zMi40ODQtODYuM3Y4Ni4zSDQzOC41NFY2MS43MjR6bTY4LjYyNS0yMy45OTRDMjQ3LjkxOCAzNy43MyAzNy43MyAyNDcuOTE4IDM3LjczIDUwNy4xNjRjMCAyNTkuMjc2IDIxMC4xODggNDY5LjQ4IDQ2OS40MzQgNDY5LjQ4IDI1OS4zMDggMCA0NjkuNDgtMjEwLjIwNCA0NjkuNDgtNDY5LjQ2NVM3NjYuNDcyIDM3LjcxNSA1MDcuMTY0IDM3LjcxNXptMCA3NDkuOTk0YzE1NC45ODYgMCAyODAuNjA3LTEyNS41OSAyODAuNjA3LTI4MC41Nkg1MDcuMTY0Vjc4Ny43NHptLTI4MC41NzYtMjgwLjU2aDI4MC41NzZWMjI2LjYxOWMtMTU0Ljk3MSAwLTI4MC41NzYgMTI1LjYwNS0yODAuNTc2IDI4MC41NDV6bTI4MC41NzYgNDg5LjYzYy0yNjkuOTU4IDAtNDg5LjY0Ni0yMTkuNjU3LTQ4OS42NDYtNDg5LjYzIDAtMjY5Ljk1OCAyMTkuNjg4LTQ4OS42MyA0ODkuNjQ2LTQ4OS42MyAyNzAuMDA1IDAgNDg5LjY0NSAyMTkuNjcyIDQ4OS42NDUgNDg5LjYzIDAgMjY5Ljk3My0yMTkuNjQgNDg5LjYzLTQ4OS42NDUgNDg5LjYzem0wLTk5Ni43OTRDMjI3LjUgMCAwIDIyNy41MDEgMCA1MDcuMTY0YzAgMjc5LjY2MiAyMjcuNTAxIDUwNy4xOTUgNTA3LjE2NCA1MDcuMTk1IDI3OS42OTMgMCA1MDcuMTc5LTIyNy41MzMgNTA3LjE3OS01MDcuMTk1QzEwMTQuMzQzIDIyNy41IDc4Ni44NTcgMCA1MDcuMTYzIDB6IiBmaWxsPSIjQjlCRUMzIi8+PC9zdmc+" />
  </fc-wave-filter>
</template>

<script>
export default {
  components: {
    // 不需要在这里进行注册 就把 <fc-wave-filter> 当成一个浏览器原生标签就行
  }
}
</script>
复制代码
```

在 vue-cli 的 Vue3 项目中
--------------------

Vue3 在写法上出现了很大的变化，没有`Vue`了，取而代之的是`app`：

```
import { createApp } from 'vue'
import App from './App'
import { FcWaveFilter } from 'fancy-components'

/* eslint-disable no-new */
new FcWaveFilter()

createApp(App).mount('#app')
复制代码
```

那么是不是把原来的`Vue`换成`app`就行了呢：

```
// 以前
Vue.config.ignoredElements = [/^fc-/]

// 现在
app.config.ignoredElements = [/^fc-/]
复制代码
```

很可惜答案是否定的，`Vue3`处理起来相对要稍微麻烦一点，我们需要在根目录下的`vue.config.js`中 (_如果没有就自己新建一个_)：

```
module.exports = {
    chainWebpack: config => {
        config.module
            .rule('vue')
            .use('vue-loader')
            .tap(options => {
                options.compilerOptions = {
                    ...(options.compilerOptions || {}),
                    isCustomElement: tag => tag.startsWith('fc-')
                }
                return options
            })
    }
}
复制代码
```

这样就可以啦！

在 Vite 的 Vue3 项目中
-----------------

> `npm i fancy-components`

来到主文件引入 [fancy-components](https://link.juejin.cn?target=https%3A%2F%2Fwww.bilibili.com%2Fvideo%2FBV1Du411Z7Sc "https://www.bilibili.com/video/BV1Du411Z7Sc") 并`new`一下：

```
import { createApp } from 'vue'
import App from './App'
import { FcWaveFilter } from 'fancy-components'

/* eslint-disable no-new */
new FcWaveFilter()

createApp(App).mount('#app')
复制代码
```

然后我们再来到`vite.config.js`中，把配置改成这样即可：

```
import { defineConfig } from 'vite'
import vue from '@vitejs/plugin-vue'

// https://vitejs.dev/config/
export default defineConfig({
  plugins: [vue({
    template: {
      compilerOptions: {
        isCustomElement: tag => tag.startsWith('fc-')
      }
    }
  })]
})
复制代码
```

各组件用法
=====

FcWaveFilter
------------

这款组件非常适合用来生成波浪形式的加载动画，在国庆期间我们就用了这款组件做成了🇨🇳样式的波浪动画。

#### API

<table><thead><tr><th align="center">CSS 属性</th><th align="center">含义</th><th align="center">可选值</th><th align="center">默认值</th></tr></thead><tbody><tr><td align="center">--width</td><td align="center">组件的宽度</td><td align="center">正常的 CSS 宽度值</td><td align="center">fit-content</td></tr><tr><td align="center">--height</td><td align="center">组件的高度</td><td align="center">正常的 CSS 高度值</td><td align="center">fit-content</td></tr><tr><td align="center">--color</td><td align="center">填充颜色</td><td align="center">正常的 CSS 颜色值</td><td align="center">black</td></tr></tbody></table>

<table><thead><tr><th align="center">标签属性</th><th align="center">含义</th><th align="center">可选值</th><th align="center">默认值</th></tr></thead><tbody><tr><td align="center">color</td><td align="center">填充颜色</td><td align="center">正常的 CSS 颜色值</td><td align="center">'black'</td></tr><tr><td align="center">dur</td><td align="center">动画时长</td><td align="center">大于 0 的数字 (单位为秒)</td><td align="center">3</td></tr><tr><td align="center">amplitude</td><td align="center">波浪幅度</td><td align="center">数字 (原波浪幅的倍数)</td><td align="center">1</td></tr><tr><td align="center">delay</td><td align="center">填充后持续的时间</td><td align="center">大于 0 的数字 (单位为秒)</td><td align="center">0</td></tr><tr><td align="center">mode</td><td align="center">动画模式</td><td align="center">'alpha'(透明度)、'luminance'(亮度)、'img'(图片)、'slideshow'(幻灯片)</td><td align="center">'alpha'</td></tr><tr><td align="center">interval</td><td align="center">几秒一切换 (适合幻灯片模式)</td><td align="center">大于 0 的数字 (单位为秒)</td><td align="center">0</td></tr></tbody></table>

但可惜的是由于某些原因我没法把🇨🇳国旗相关的图片贴上来，那么我们干脆换一个别的国家的国旗给大家展示一下效果。找个跟咱们比较友好的国家的国旗吧！哪个国家跟咱们关系好？首先想到的肯定是：🇵🇰

但这面🇵🇰左侧有较大一片空白，导致波浪效果不明显，所以我们再换一面旗，换成这个吧：🇪🇺

![](https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/c5a7ebb30aa640199592bd88c1669d65~tplv-k3u1fbpfcp-watermark.awebp?)

其实上面的效果跟中国国旗的效果比起来还是差了不少，这款 [FcWaveFilter](https://juejin.cn/post/7005763586999975943 "https://juejin.cn/post/7005763586999975943") 组件的具体用法我已经在之前的：[《波浪动画很常见，但这个波浪组件绝对不常见》](https://juejin.cn/post/7005763586999975943 "https://juejin.cn/post/7005763586999975943")这篇文章里非常详细的介绍过了，所以就不占用本篇文章的篇幅了，感兴趣的朋友可以[点进去看看](https://juejin.cn/post/7005763586999975943 "https://juejin.cn/post/7005763586999975943")。

这里只讲一下基本用法，就是我们找一张纯色 (最好为浅灰色) 的`png`镂空图片，比方说我们找了张这个图：

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/6bf8ca10e73f4c62b6c21078df90eeea~tplv-k3u1fbpfcp-watermark.awebp)

我们只需要用`<fc-wave-filter>`这个标签把图片包起来，并给一个`color`属性作为填充颜色即可：

```
<template>
  <fc-wave-filter color="#047">
    <img src="bwm.png" />
  </fc-wave-filter>
</template>

<script>
import { FcWaveFilter } from 'fancy-components'

/* eslint-disable no-new */
new FcWaveFilter()

export default {}
复制代码
```

![](https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/48ada661782b432595893c6a422c73e3~tplv-k3u1fbpfcp-watermark.awebp)

FcChina
-------

听名字也能猜到这是一款什么组件了吧？不过还是由于那个原因，这篇文章没法上传与🇨🇳有关的图片，所以大家只能点进[这个链接](https://link.juejin.cn?target=https%3A%2F%2Ffancy-components.github.io%2F%23%2F "https://fancy-components.github.io/#/")去看看这炫酷的国旗特效了，放在`7.1`、`10.1`这种日子里作为开场动画效果简直吊炸天。

#### API

<table><thead><tr><th align="center">CSS 属性</th><th align="center">含义</th><th align="center">可选值</th><th align="center">默认值</th></tr></thead><tbody><tr><td align="center">--width</td><td align="center">组件的宽度</td><td align="center">正常的 CSS 宽度值</td><td align="center">300px</td></tr><tr><td align="center">--height</td><td align="center">组件的高度</td><td align="center">正常的 CSS 高度值</td><td align="center">200px</td></tr><tr><td align="center">--star-stroke-color</td><td align="center">五颗五角星在执行描边动画时的描边颜色</td><td align="center">正常的 CSS 颜色值</td><td align="center">red</td></tr><tr><td align="center">--star-fill-color</td><td align="center">五颗五角星在执行填充动画时的填充颜色</td><td align="center">正常的 CSS 颜色值</td><td align="center">red</td></tr><tr><td align="center">--flag-stroke-color</td><td align="center">当国旗执行描边动画时的描边颜色</td><td align="center">正常的 CSS 颜色值</td><td align="center">red</td></tr><tr><td align="center">--dalay</td><td align="center">延时执行动画</td><td align="center">正常的 CSS 时间值</td><td align="center">0s</td></tr><tr><td align="center">--dur</td><td align="center">整段动画的执行时间</td><td align="center">正常的 CSS 时间值</td><td align="center">6.6s</td></tr></tbody></table>

```
<template>
  <fc-china></fc-china>
</template>

<script>
import { FcChina } from 'fancy-components'

/* eslint-disable no-new */
new FcChina()

export default {}
</script>
复制代码
```

它的默认宽高是`300px`x`200px`，我们可以通过`--width`和`--height`这两个 [CSS 自定义属性 (又称 CSS 变量)](https://juejin.cn/post/6856668819344392206#heading-1 "https://juejin.cn/post/6856668819344392206#heading-1") 来改变它的宽高：

```
<template>
  <fc-china></fc-china>
</template>

<script>
import { FcChina } from 'fancy-components'

/* eslint-disable no-new */
new FcChina()

export default {}
</script>

<style>
fc-china {
  --width: 600px;
  --height: 200px;
}
</style>
复制代码
```

> (本文没法贴🇨🇳图片，这里的效果就是国旗被拉伸导致五颗五角星⭐️变形了)

变得这么难看的原因是因为我们给的宽高没有按照`3:2`的这么一个宽高来，中国国旗🇨🇳的默认宽高比就是`3:2`，我们还可以修改动画运行时的颜色：

```
<template>
  <fc-china></fc-china>
</template>

<script>
import { FcChina } from 'fancy-components'

/* eslint-disable no-new */
new FcChina()

export default {}
</script>

<style>
fc-china {
  --star-stroke-color: black;
  --star-fill-color: aqua;
  --flag-stroke-color: #ff8ef9;
}
</style>
复制代码
```

> (本文没法贴🇨🇳图片，这里的效果就是可以生成其他的颜色的五角星，描边动画的颜色也可以换，只不过动画快要结束时五星红旗依然会恢复经典的红黄配色)

这里只能修改动画运行时的颜色，动画结束后国旗还是会回归到它原本的红黄配色，毕竟国旗神圣，不可随意修改颜色嘛！我们准备监听它身上的`animationend`事件，当国旗动画一结束我们就写一个背景扩散的效果来回到首页：

```
<template>
  <fc-china @animationend="onEnd"></fc-china>
</template>

<script>
import { FcChina } from 'fancy-components'

/* eslint-disable no-new */
new FcChina()

export default {
  methods: {
    onEnd () {
      // 在这里写一些逻辑
    }
  }
}
</script>
复制代码
```

FcWarpBtn
---------

这是我最喜欢的一款按钮组件：

![](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/e8c4515a6eea4bbe956bfcb138349335~tplv-k3u1fbpfcp-watermark.awebp?)

#### API

<table><thead><tr><th align="center">CSS 属性</th><th align="center">含义</th><th align="center">取值范围</th><th align="center">默认值</th></tr></thead><tbody><tr><td align="center">--width</td><td align="center">组件宽度</td><td align="center">正常的 CSS 宽度值</td><td align="center">200px</td></tr><tr><td align="center">--height</td><td align="center">组件高度</td><td align="center">正常的 CSS 高度值</td><td align="center">40px</td></tr><tr><td align="center">--color</td><td align="center">组件颜色</td><td align="center">正常的 CSS 颜色值</td><td align="center">#1cd</td></tr><tr><td align="center">--shadow-color</td><td align="center">组件阴影颜色</td><td align="center">正常的 CSS 颜色值</td><td align="center">rgba(0, 0, 0, .5)</td></tr></tbody></table>

<table><thead><tr><th align="center">标签属性</th><th align="center">含义</th><th align="center">取值范围</th><th align="center">默认值</th></tr></thead><tbody><tr><td align="center">text-align</td><td align="center">文本排列方式</td><td align="center">'left'、'center'、'right'</td><td align="center">'right'</td></tr></tbody></table>

```
<template>
  <fc-warp-btn></fc-warp-btn>
</template>

<script>
import { FcWarpBtn } from 'fancy-components'

/* eslint-disable no-new */
new FcWarpBtn()

export default {}
</script>
复制代码
```

我们可以通过`--color`这个 [CSS 自定义属性 (又称 CSS 变量)](https://juejin.cn/post/6856668819344392206#heading-1 "https://juejin.cn/post/6856668819344392206#heading-1") 来改变它的整体颜色：

```
<template>
  <fc-warp-btn></fc-warp-btn>
</template>

<script>
import { FcWarpBtn } from 'fancy-components'

/* eslint-disable no-new */
new FcWarpBtn()

export default {}
</script>

<style>
fc-warp-btn {
  --color: gold
}
</style>
复制代码
```

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/5e54737297b04068b4d3daf12386740f~tplv-k3u1fbpfcp-watermark.awebp?)

想要改变里面的文字的话就在标签里面写：

```
<fc-warp-btn>翘边按钮</fc-warp-btn>
复制代码
```

![](https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/82dd945c601b4964ad9460517e3142b0~tplv-k3u1fbpfcp-watermark.awebp?)

我们发现文字默认是居左的，在文字比较少的情况下居左并不好看，所以我们可以用`text-align`属性来给它变成居中的：

```
<fc-warp-btn text-align="center">翘边按钮</fc-warp-btn>
复制代码
```

![](https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/f463cb8f009a4021ba68cf1184288fc8~tplv-k3u1fbpfcp-watermark.awebp?)

其实还是不怎么好看，我们用`--width`来给它变短一点吧：

```
<template>
  <fc-warp-btn text-align="center">翘边按钮</fc-warp-btn>
</template>

<script>
import { FcWarpBtn } from 'fancy-components'

/* eslint-disable no-new */
new FcWarpBtn()

export default {}
</script>

<style>
fc-warp-btn {
  --width: 160px
}
</style>
复制代码
```

![](https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/fe70d06e5b094dbd80467c7dab0a0b4a~tplv-k3u1fbpfcp-watermark.awebp?)

个人感觉这个按钮不太适合文字较短的按钮，因为这个按钮就是长了才好看，短了就不好看了。另外，`--shadow-color`指的是这里的阴影颜色：

![](https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/663925de59c047c1afd9a708855dd456~tplv-k3u1fbpfcp-watermark.awebp?)

Fc3DBtn
-------

![](https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/c05dde8de65e44ee83cfbfd3087b76bf~tplv-k3u1fbpfcp-watermark.awebp?)

#### API

<table><thead><tr><th align="center">CSS 属性</th><th align="center">含义</th><th align="center">取值范围</th><th align="center">默认值</th></tr></thead><tbody><tr><td align="center">--width</td><td align="center">组件宽度</td><td align="center">正常的 CSS 宽度值</td><td align="center">100px</td></tr><tr><td align="center">--height</td><td align="center">组件高度</td><td align="center">正常的 CSS 高度值</td><td align="center">36px</td></tr><tr><td align="center">--color</td><td align="center">组件颜色</td><td align="center">正常的 CSS 颜色值</td><td align="center">#a69</td></tr><tr><td align="center">--cover-color</td><td align="center">组件凸起的那部分颜色</td><td align="center">正常的 CSS 颜色值</td><td align="center">#0005</td></tr><tr><td align="center">--shadow-color</td><td align="center">组件阴影颜色</td><td align="center">正常的 CSS 颜色值</td><td align="center">#0008</td></tr><tr><td align="center">--inset-shadow-color</td><td align="center">组件内阴影颜色</td><td align="center">正常的 CSS 颜色值</td><td align="center">#fffc</td></tr><tr><td align="center">--inset-shadow-color-active</td><td align="center">组件内阴影在按钮处于点击状态时的颜色</td><td align="center">正常的 CSS 颜色值</td><td align="center">var(--inset-shadow-color)</td></tr></tbody></table>

这个按钮我很喜欢，我们来给它换个颜色换个文字看看：

```
<template>
  <div class="container">
    <fc-3d-btn>立体按钮</fc-3d-btn>
  </div>
</template>

<script>
import { Fc3DBtn } from 'fancy-components'

/* eslint-disable no-new */
new Fc3DBtn()

export default {}
</script>

<style scoped>
.container {
  padding: 300px;
  background: #4d317a;
}

fc-3d-btn {
  --color: #6e50a6;
  --shadow-color: rgba(255, 255, 255, .4);
  --cover-color: rgba(0, 0, 0, .4);
  --inset-shadow-color: rgb(29, 4, 54);
  --inset-shadow-color-active: rgba(49, 23, 75, .9);
}
</style>
复制代码
```

![](https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/894a15e215c1479096e7149bcef36e03~tplv-k3u1fbpfcp-watermark.awebp?)

![](https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/e0a86772bfe048dbbf06b04fffc7e9d1~tplv-k3u1fbpfcp-watermark.awebp?)

FcDblWarpBtn
------------

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/c76733471756457f8e9f860794f13c6e~tplv-k3u1fbpfcp-watermark.awebp?)

#### API

<table><thead><tr><th align="center">CSS 属性</th><th align="center">含义</th><th align="center">取值范围</th><th align="center">默认值</th></tr></thead><tbody><tr><td align="center">--width</td><td align="center">组件宽度</td><td align="center">正常的 CSS 宽度值</td><td align="center">180px</td></tr><tr><td align="center">--height</td><td align="center">组件高度</td><td align="center">正常的 CSS 高度值</td><td align="center">40px</td></tr><tr><td align="center">--color</td><td align="center">组件颜色</td><td align="center">正常的 CSS 颜色值</td><td align="center">#1cd</td></tr><tr><td align="center">--shadow-color</td><td align="center">阴影颜色</td><td align="center">正常的 CSS 颜色值</td><td align="center">#0005</td></tr></tbody></table>

```
<template>
  <fc-dbl-warp-btn></fc-dbl-warp-btn>
</template>

<script>
import { FcDblWarpBtn } from 'fancy-components'

/* eslint-disable no-new */
new FcDblWarpBtn()

export default {}
</script>
复制代码
```

这个双翘边按钮的名字确实是有点太长了，我们其实是可以在注册组件的时候为其改名的，只需要在`new`的时候传进一个字符串即可，这个字符串便会成为标签的名字。不过名字也不是瞎起的，还是需要遵守`web components`的命名规范的，比如必须字母开头，必须有短横线进行连接等。这么写效果也是完全一样的：

```
<template>
  <fc-btn></fc-btn>
</template>

<script>
import { FcDblWarpBtn } from 'fancy-components'

/* eslint-disable no-new */
new FcDblWarpBtn('fc-btn')

export default {}
</script>
复制代码
```

![](https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/bda1ba4a8b734dd785079499688563ba~tplv-k3u1fbpfcp-watermark.awebp?)

理论上来说我们采用啥字母开头都行，但如果是在`Vue`项目中，我们配置的不是忽略`fc-`开头的元素么，所以起的新名最好也是`fc-`开头。注意不要与其他组件重名。

FcUnderlineBtn、FcPixelBtn、FcParenthesesBtn、FcRoundBtn
-----------------------------------------------------

#### FcUnderlineBtn API

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/5043e055a83a444b8a5f06298b713dcd~tplv-k3u1fbpfcp-watermark.awebp?)

<table><thead><tr><th align="center">CSS 属性</th><th align="center">含义</th><th align="center">取值范围</th><th align="center">默认值</th></tr></thead><tbody><tr><td align="center">--width</td><td align="center">组件宽度</td><td align="center">正常的 CSS 宽度值</td><td align="center">100px</td></tr><tr><td align="center">--height</td><td align="center">组件高度</td><td align="center">正常的 CSS 高度值</td><td align="center">30px</td></tr><tr><td align="center">--color</td><td align="center">组件颜色</td><td align="center">正常的 CSS 颜色值</td><td align="center">#1cd</td></tr></tbody></table>

#### FcPixelBtn API

![](https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/d93fe983ce064f468fc4afa349d3a204~tplv-k3u1fbpfcp-watermark.awebp?)

<table><thead><tr><th align="center">CSS 属性</th><th align="center">含义</th><th align="center">取值范围</th><th align="center">默认值</th></tr></thead><tbody><tr><td align="center">--width</td><td align="center">组件宽度</td><td align="center">正常的 CSS 宽度值</td><td align="center">100px</td></tr><tr><td align="center">--height</td><td align="center">组件高度</td><td align="center">正常的 CSS 高度值</td><td align="center">40px</td></tr><tr><td align="center">--color</td><td align="center">组件颜色</td><td align="center">正常的 CSS 颜色值</td><td align="center">#1cd</td></tr></tbody></table>

#### FcParenthesesBtn API

![](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/8260096ead49486d8c497a460a2bc14b~tplv-k3u1fbpfcp-watermark.awebp?)

<table><thead><tr><th align="center">CSS 属性</th><th align="center">含义</th><th align="center">取值范围</th><th align="center">默认值</th></tr></thead><tbody><tr><td align="center">--width</td><td align="center">组件宽度</td><td align="center">正常的 CSS 宽度值</td><td align="center">80px</td></tr><tr><td align="center">--height</td><td align="center">组件高度</td><td align="center">正常的 CSS 高度值</td><td align="center">30px</td></tr><tr><td align="center">--color</td><td align="center">组件颜色</td><td align="center">正常的 CSS 颜色值</td><td align="center">#1cd</td></tr></tbody></table>

#### FcRoundBtn API

![](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/6d98281794fb422eab7d5cec6ad2e7d2~tplv-k3u1fbpfcp-watermark.awebp?)

<table><thead><tr><th align="center">CSS 属性</th><th align="center">含义</th><th align="center">取值范围</th><th align="center">默认值</th></tr></thead><tbody><tr><td align="center">--width</td><td align="center">组件宽度</td><td align="center">正常的 CSS 宽度值</td><td align="center">100px</td></tr><tr><td align="center">--height</td><td align="center">组件高度</td><td align="center">正常的 CSS 高度值</td><td align="center">40px</td></tr><tr><td align="center">--color</td><td align="center">组件颜色</td><td align="center">正常的 CSS 颜色值</td><td align="center">#1cd</td></tr></tbody></table>

这几款按钮的`API`都一模一样，所以只拿一个举例就够了：

```
<template>
  <fc-underline-btn></fc-underline-btn>
</template>

<script>
import { FcUnderlineBtn } from 'fancy-components'

/* eslint-disable no-new */
new FcUnderlineBtn()

export default {}
</script>
复制代码
```

除了`--width`和`--height`这俩控制宽高的 [CSS 自定义属性](https://juejin.cn/post/6856668819344392206#heading-1 "https://juejin.cn/post/6856668819344392206#heading-1")外，还有一个`--color`可以控制整体颜色：

```
<template>
  <fc-underline-btn></fc-underline-btn>
</template>

<script>
import { FcUnderlineBtn } from 'fancy-components'

/* eslint-disable no-new */
new FcUnderlineBtn()

export default {}
</script>

<style>
fc-underline-btn {
  --color: red
}
</style>
复制代码
```

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/9fbd3a9ca2b14f03a080bc72cfd7da76~tplv-k3u1fbpfcp-watermark.awebp?)

FcArrowBtn
----------

![](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/7d946fe4ddef458390391c6d1f696743~tplv-k3u1fbpfcp-watermark.awebp?)

#### API

<table><thead><tr><th align="center">CSS 属性</th><th align="center">含义</th><th align="center">取值范围</th><th align="center">默认值</th></tr></thead><tbody><tr><td align="center">--width</td><td align="center">组件宽度</td><td align="center">正常的 CSS 宽度值</td><td align="center">100px</td></tr><tr><td align="center">--height</td><td align="center">组件高度</td><td align="center">正常的 CSS 高度值</td><td align="center">30px</td></tr><tr><td align="center">--color</td><td align="center">组件颜色</td><td align="center">正常的 CSS 颜色值</td><td align="center">#1cd</td></tr></tbody></table>

<table><thead><tr><th align="center">标签属性</th><th align="center">含义</th><th align="center">可选值</th><th align="center">默认值</th></tr></thead><tbody><tr><td align="center">right</td><td align="center">箭头朝右</td><td align="center">写或不写</td><td align="center">不写</td></tr></tbody></table>

箭头按钮跟上面的那几个按钮一样也是只有三个 [CSS 自定义属性](https://juejin.cn/post/6856668819344392206#heading-1 "https://juejin.cn/post/6856668819344392206#heading-1")，只不过就是多了一个名为`right`的标签属性，箭头默认不是朝左的么，加上`right`就朝右了：

```
<template>
  <fc-arrow-btn right>下一步</fc-arrow-btn>
</template>

<script>
import { FcArrowBtn } from 'fancy-components'

/* eslint-disable no-new */
new FcArrowBtn()

export default {}
</script>
复制代码
```

![](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/ee75d564675d4578b878faf6f06e1986~tplv-k3u1fbpfcp-watermark.awebp?)

FcTypingInput
-------------

![](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/e4b5ad7dc0784525bfbee949c76bcd1a~tplv-k3u1fbpfcp-watermark.awebp?)

#### API

<table><thead><tr><th align="center">CSS 属性</th><th align="center">含义</th><th align="center">可选值</th><th align="center">默认值</th></tr></thead><tbody><tr><td align="center">--width</td><td align="center">组件的宽度</td><td align="center">正常的 CSS 宽度值</td><td align="center">220px</td></tr><tr><td align="center">--height</td><td align="center">组件的高度</td><td align="center">正常的 CSS 高度值</td><td align="center">40px</td></tr><tr><td align="center">--color</td><td align="center">整体颜色</td><td align="center">正常的 CSS 颜色值</td><td align="center">#caf</td></tr><tr><td align="center">--border-color</td><td align="center">边框颜色</td><td align="center">正常的 CSS 颜色值</td><td align="center">#caf</td></tr><tr><td align="center">--border-color-hover</td><td align="center">hover 状态下的边框颜色</td><td align="center">正常的 CSS 颜色值</td><td align="center">var(--color)</td></tr><tr><td align="center">--border-color-focus</td><td align="center">focus 状态下的边框颜色</td><td align="center">正常的 CSS 颜色值</td><td align="center">var(--border-color-hover)</td></tr><tr><td align="center">--border-radius</td><td align="center">圆角</td><td align="center">正常的 CSS 圆角值</td><td align="center">4px</td></tr><tr><td align="center">--box-shadow-focus</td><td align="center">在 focus 状态下的 input 框盒阴影</td><td align="center">正常的 CSS 阴影值</td><td align="center">0 0 6px var(--border-color-focus)</td></tr><tr><td align="center">--circle-color</td><td align="center">input 框在无值并且非 focus 状态时里面的那个小圆点的颜色</td><td align="center">正常的 CSS 颜色值</td><td align="center">#0003</td></tr><tr><td align="center">--circle-color-hover</td><td align="center">input 框在无值并处于 hover 状态下里面的那个小圆点的颜色</td><td align="center">正常的 CSS 颜色值</td><td align="center">var(--circle-color)</td></tr><tr><td align="center">--placeholder-color</td><td align="center">placeholder 的颜色</td><td align="center">正常的 CSS 颜色值</td><td align="center">#0005</td></tr><tr><td align="center">--placeholder-color-focus</td><td align="center">placeholder 在 input 框处于 focus 状态时的颜色</td><td align="center">正常的 CSS 颜色值</td><td align="center">var(--border-color-focus)</td></tr><tr><td align="center">--placeholder-animate-color</td><td align="center">placeholder 在 input 框内执行动画时的颜色</td><td align="center">正常的 CSS 颜色值</td><td align="center">red</td></tr><tr><td align="center">--placeholder-animate-title-color</td><td align="center">placeholder 在 input 框的头上执行动画时的颜色</td><td align="center">正常的 CSS 颜色值</td><td align="center">#00ff6b</td></tr><tr><td align="center">--placeholder-shadow</td><td align="center">placeholder 的文本阴影</td><td align="center">正常的 CSS 文本阴影值</td><td align="center">none</td></tr><tr><td align="center">--stripe-color</td><td align="center">input 框在 disabled 状态时产生的条纹颜色</td><td align="center">正常的 CSS 颜色值</td><td align="center">#0003</td></tr><tr><td align="center">--stripe-deg</td><td align="center">input 框在 disabled 状态时产生的条纹角度</td><td align="center">数字</td><td align="center">45</td></tr><tr><td align="center">--disabled-filter</td><td align="center">input 框在 disabled 状态时的滤镜</td><td align="center">正常的 CSS 滤镜值</td><td align="center">opacity(80%) grayscale(100%)</td></tr></tbody></table>

<table><thead><tr><th align="center">标签属性</th><th align="center">含义</th><th align="center">可选值</th><th align="center">默认值</th></tr></thead><tbody><tr><td align="center">white</td><td align="center">input 框的主题颜色为白色 (适用于暗色背景)</td><td align="center">写或不写</td><td align="center">不写</td></tr><tr><td align="center">red</td><td align="center">input 框的主题颜色为红色 (适用于校验不通过时)</td><td align="center">写或不写</td><td align="center">不写</td></tr></tbody></table>

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/1bbabd7845694d6b92fab2213dbecc3b~tplv-k3u1fbpfcp-watermark.awebp?)

我们给它加个`disabled`属性看看会变成什么样：

```
<template>
  <fc-typing-input placeholder="UserName" disabled></fc-typing-input>
</template>

<script>
import { FcTypingInput } from 'fancy-components'

/* eslint-disable no-new */
new FcTypingInput()

export default {}
</script>
复制代码
```

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/c1f2e95d37224c15b9443d16c4ca6b21~tplv-k3u1fbpfcp-watermark.awebp?)

这些 [CSS 自定义属性](https://juejin.cn/post/6856668819344392206#heading-1 "https://juejin.cn/post/6856668819344392206#heading-1")看似很多很不好记，实则大部分情况压根儿就用不到，因为我们的背景最常见的一个是这种白色为主的亮色模式，另一个就是暗色模式了，比方说我们把背景色换成暗紫色来看看：

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/6615eaff6fad4c6c9d249d8ae0eddc91~tplv-k3u1fbpfcp-watermark.awebp?)

看起来效果很不明显对吧？我们难道还要挨个去调各种颜色吗？当然不用，有一个快捷的 [CSS 自定义属性](https://juejin.cn/post/6856668819344392206#heading-1 "https://juejin.cn/post/6856668819344392206#heading-1")叫`--color`，设置它一个就可以把字体颜色、`placeholder`颜色、圆圈颜色 (hover)、边框颜色(hover) 等一系列颜色设置了：

```
<template>
  <fc-typing-input placeholder="UserName"></fc-typing-input>
</template>

<script>
import { FcTypingInput } from 'fancy-components'

/* eslint-disable no-new */
new FcTypingInput()

export default {}
</script>

<style>
fc-typing-input {
  --color: black
}
</style>
复制代码
```

![](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/85810e33d8194147ba4da2f82f2cacb3~tplv-k3u1fbpfcp-watermark.awebp?)

还有一个适合在暗色背景下用的标签属性`white`，写上了它就会自动帮我们把各种边框颜色各种字体颜色调成白色：

```
<fc-typing-input placeholder="UserName" white></fc-typing-input>
复制代码
```

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/dcf79a8a35cf4e02a930d644f765f18a~tplv-k3u1fbpfcp-watermark.awebp?)

在默认情况下`placeholder`本来是没有文本阴影的，但加上了`white`这个标签属性后它就会为我们自动设置一个白色的文本阴影，看起来就像是文字在发光一样，这在暗色模式下非常好看，如果不喜欢的话也可以直接把`--placeholder-shadow-color`设置为`none`，然后再把`--placeholder-color`换个浅一点的颜色：

```
<template>
  <fc-typing-input placeholder="UserName" white></fc-typing-input>
</template>

<script>
import { FcTypingInput } from 'fancy-components'

/* eslint-disable no-new */
new FcTypingInput()

export default {}
</script>

<style>
body {
  background: #3f2766
}

fc-typing-input {
  --placeholder-color: #fff8;
  --placeholder-shadow: none;
}
</style>
复制代码
```

![](https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/1cf442dbadf2488c8b16305d5520b1b9~tplv-k3u1fbpfcp-watermark.awebp?)

这样看起来确实不怎么好看，所以那个`white`属性应该是精心配置了各个颜色的值，以达到最佳效果。

除了`white`还有个`red`，`white`不是让整体颜色变白么？那`red`就是让整体颜色变红：

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/dd50c994dd9f4131a5bb17d32c08de20~tplv-k3u1fbpfcp-watermark.awebp?)

一般用于表单校验没通过时。那`--placeholder-animate-color`和`--placeholder-animate-title-color`这两个属性是干嘛的呢？我们在白色背景下给标签加上一个`white`属性：

![](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/4230426312234e00a2692b825f88ff23~tplv-k3u1fbpfcp-watermark.awebp?)

什么也没有？这是因为我们的边框是白色的、字体是白色的、`placeholder`也是白色的，这跟白色的背景融为一体了，不过我们用鼠标点击一下再看看：

![](https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/d5a018051a424779974780f5a5788f88~tplv-k3u1fbpfcp-watermark.awebp?)

这回看到没？`placeholder`在动画执行时是会变一下颜色的，`--placeholder-animate-color`是在框里执行动画时会变的颜色，而`--placeholder-animate-title-color`是在框上面 (也就是 title 位) 执行动画时会变的颜色。(大部分情况下都不会用到)

比较遗憾的一点是这个输入框在`Vue`中不能使用`v-model`，因为`Vue`检测到他不是`input`元素，不过我们只需要写成这样即可：

```
<template>
  <fc-typing-input :value="value" @input="onInput"></fc-typing-input>
</template>

<script>
import { FcTypingInput } from 'fancy-components'

/* eslint-disable no-new */
new FcTypingInput()

export default {
  data: () => ({ value: '666' }),
  methods: {
    onInput (event) {
      this.value = event.target.value
    }
  }
}
</script>
复制代码
```

⚠️该组件不支持`change`事件 并且`type`属性只支持`text`和`password`：

```
<template>
  <fc-typing-input
    :value="value"
    @input="onInput"
    type="password"
    placeholder="请输入密码"
  ></fc-typing-input>
</template>

<script>
import { FcTypingInput } from 'fancy-components'

/* eslint-disable no-new */
new FcTypingInput()

export default {
  data: () => ({ value: '666666' }),
  methods: {
    onInput (event) {
      this.value = event.target.value
    }
  }
}
</script>
复制代码
```

![](https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/e5a617d5b1004c55be81ee7706144ed5~tplv-k3u1fbpfcp-watermark.awebp?)

FcBubbles
---------

#### API

<table><thead><tr><th align="center">CSS 属性</th><th align="center">含义</th><th align="center">可选值</th><th align="center">默认值</th></tr></thead><tbody><tr><td align="center">--color</td><td align="center">填充颜色</td><td align="center">正常的 CSS 颜色值</td><td align="center">#1cd</td></tr><tr><td align="center">--width</td><td align="center">组件的宽度</td><td align="center">正常的 CSS 宽度值</td><td align="center">fit-content</td></tr><tr><td align="center">--height</td><td align="center">组件的高度</td><td align="center">正常的 CSS 高度值</td><td align="center">fit-content</td></tr></tbody></table>

<table><thead><tr><th align="center">标签属性</th><th align="center">含义</th><th align="center">可选值</th><th align="center">默认值</th></tr></thead><tbody><tr><td align="center">active</td><td align="center">通过 active 属性来控制气泡动效的显隐</td><td align="center">'true'、'false'</td><td align="center">'false'</td></tr><tr><td align="center">click</td><td align="center">通过鼠标点击来控制气泡动效的显隐</td><td align="center">写或不写</td><td align="center">不写</td></tr></tbody></table>

这个组件非常好玩，它和 [FcWaveFilter](https://juejin.cn/post/7005763586999975943 "https://juejin.cn/post/7005763586999975943") 一样，都是自己本身不产生效果，而是为子元素添加效果，用`active`属性来控制气泡效果，比如说好多网站都有❤️这么一个效果：

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/0e03948c98c249a29766fe1a32221512~tplv-k3u1fbpfcp-watermark.awebp?)

这个小心心❤️很可爱吧？但它没有任何的动画，所以有些略显单调，此时我们就用`<fc-bubbles>`来把它包裹起来：

```
<template>
  <fc-bubbles
    :active="isLike"
    @click="isLike = !isLike"
  >
    <svg viewBox="0 0 1024 1024" width="100" height="100">
      <path
        d="M288.105 122.229C174.047 122.21 64.691 231.687 64.691 388.095c0 176.07 156.747 241.891 261.855 312.283 101.962 68.266 174.057 161.602 185.453 201.393 9.743-38.936 91.03-135.019 185.448-203.373 103.196-74.727 261.861-136.19 261.861-312.262 0-152.117-109.34-260.294-222.908-260.294-86.792 0-167.781 15.754-224.419 128.441-65.593-111.977-138.495-132.042-223.876-132.054"
        stroke="black"
        stroke-width="22"
        :fill="isLike ? 'red' : 'none'"
      />
     </svg>
  </fc-bubbles>
</template>

<script setup>
import { ref } from 'vue'

const isLike = ref(false)
</script>

<style scoped>
fc-bubbles { --color: #ff3874 }
svg { display: block }
</style>
复制代码
```

![](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/a8dba24910b04047bf38e76b6a7f4bee~tplv-k3u1fbpfcp-watermark.awebp?)

上面这种情况是需要靠`active`这个属性来控制动画的出现时机，当`active="true"`时运行气泡动画，`active="false"`时停止动画。

> ⚠️ 只有`active="false"`时再把`active`变成`true`才会有效果。

这是什么意思呢？比如我们想要把这个`<fc-bubbles>`放在某个按钮上，想要一点按钮就出现效果，那么如果我们写成这样：

```
<template>
  <fc-bubbles @click="handleClick" :active="isActive">
    <fc-3d-btn></fc-3d-btn>
  </fc-bubbles>
</template>

<script setup>
import { ref } from '@vue/runtime-core'
import { FcBubbles, Fc3DBtn } from 'fancy-components';

/* eslint-disable no-new */
new FcBubbles()
new Fc3DBtn()

const isActive = ref(false)
const handleClick = () => isActive.value = true
</script>

<style scoped>
fc-bubbles {
  --color: #6195f9;
}
</style>
复制代码
```

![](https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/485aa297653a47b589fca9cd71a92229~tplv-k3u1fbpfcp-watermark.awebp?)

发现只有第一次点击时有效果，剩下的点击啥效果也没有，这是因为第一次点击之前`active`的值为`false`，第一次点击时把它变成了`true`，但却没有把它变回`false`，于是`active`就一直为`true`了，相当于第一次点击过后这个值就一直都没有发生任何的变化了，所以才不会有效果。

那我们把`handleClick`这个函数写成这样不就行了：

```
const handleClick = () => {
  isActive.value = true
  isActive.value = false
}
复制代码
```

注意这种写法并不可取，因为`active="true"`时动画才刚出现，紧接着就把`active`变成了`false`，动画就会消失掉，看起来就跟什么效果都没出现一样。可是我们就是想在点击时出现个气泡动画罢了，有必要搞得这么麻烦么？当然没必要啦！因为有个`click`属性是专门是干这个的：

```
<template>
  <!-- 在 Vue3 中需要把 click 的首字母大写才会生效 -->
  <fc-bubbles click>
    <fc-3d-btn></fc-3d-btn>
  </fc-bubbles>
</template>

<script>
import { FcBubbles, Fc3DBtn } from 'fancy-components';

/* eslint-disable no-new */
new FcBubbles()
new Fc3DBtn()
</script>

<style scoped>
fc-bubbles {
  --color: #6195f9;
}
</style>
复制代码
```

![](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/fde6094a50664816b240d05a1cec5f0f~tplv-k3u1fbpfcp-watermark.awebp?)

在`<fc-bubbles>`这个标签上写上一个`click`属性就可以让每次点击时都出现气泡效果。由于气泡出现的时机变成了`点击时出现`，所以就不再需要`active`这个属性来控制时机了，`click`和`active`这两个属性最好不要同时出现，不然会有`bug`。

在`Vue3`里有这样一个`bug`：在一个标签中写`click`属性：

```
<template>
  <fc-bubbles click></fc-bubbles>
</template>
复制代码
```

编译后这个`click`属性却并没有出现在这个标签上，并且页面上也没有任何的报错，而`Vue2`就没有这个`bug`，可是我们就是想在`Vue3`里写这个`click`属性怎么办呢？答案就是把`click`的首字母大写：

```
<template>
  <fc-bubbles Click></fc-bubbles>
</template>
复制代码
```

这样就不会有问题啦！

之前在公司的多个页面都用过这一效果：

![](https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/cd79ef8a3f3746debd4d831863e6e832~tplv-k3u1fbpfcp-watermark.awebp)

这个老虎机效果用的是[《产品经理：能不能让这串数字滚动起来？》](https://juejin.cn/post/6986453616517185567 "https://juejin.cn/post/6986453616517185567")这篇文章里的组件，当时的需求是三位数字滚动，当滚动停止时的数字如果为`001`，就会变成`1st`、`002`就是`2nd`、`003`变`3rd`、剩下的数字就是 xxx`th`。而且后面的那个旗的颜色一开始是灰色，只有滚动的数字停下来的时候才会知道第几名，前 3 名旗的颜色是这个色、前十是那个色、前 100 又换个别的色、100 之后又是个什么色……

总之，当数字停下来后会有一系列的突变 (文字 + 颜色)，效果十分突兀，必须要有个什么动画来掩饰一下，于是就用了`<fc-bubbles>`这个组件，没想到效果出奇的好，当时监听了`animationend`这个事件，当`996`的动画一停住就触发这个事件，紧接着把`active`调成`true`，效果就这样完成啦！

大家如果碰到那种比较突兀的变化时可以试着用这个组件来掩饰一下，效果还算不错。

这个效果我曾在`GitHub`上看到过，看起来应该是类似的实现方式，地址贴到[这里](https://link.juejin.cn?target=https%3A%2F%2Fgithub.com%2FXboxYan%2Fnotes%2Fissues%2F16 "https://github.com/XboxYan/notes/issues/16")，感兴趣的可以点进去看看。

fancy-components 仓库地址
=====================

> [github.com/fancy-compo…](https://link.juejin.cn?target=https%3A%2F%2Fgithub.com%2Ffancy-components%2Ffancy-components "https://github.com/fancy-components/fancy-components")

这么酷的项目居然没有多少`star`，可能是因为刚刚起步还没有多少人知道吧？不过我这一波推广应该能让不少人知道。就像`Naive UI`那样，尤大发个微博发个推特，立马涨了好几千`star`，只是我应该没尤大那么牛 B😂

往期精彩文章
======

*   [《移动端布局面试题 全面考察你的 CSS 功底 (居中篇)》](https://juejin.im/post/6884971597498613768 "https://juejin.im/post/6884971597498613768")
*   [《在 Vue 项目中使用 React 超火的 CSS-in-JS 库: styled-components》](https://juejin.im/post/6844904030641078280 "https://juejin.im/post/6844904030641078280")
*   [《不依赖任何库打造属于自己的可视化数据地图》](https://juejin.im/post/6865591917279870990 "https://juejin.im/post/6865591917279870990")
*   [《Vue 第二波 ref 语法提案来袭 这次会进入到标准吗？》](https://juejin.cn/post/6997186627781001229 "https://juejin.cn/post/6997186627781001229")
*   [《尤雨溪国外教程：亲手带你写个简易版的 Vue！》](https://juejin.cn/post/6992018709439053837 "https://juejin.cn/post/6992018709439053837")
*   [《产品经理：鸿蒙那个开场动画挺帅的 给咱们页面也整一个呗》](https://juejin.cn/post/6979042510400126983 "https://juejin.cn/post/6979042510400126983")
*   [《[译] 尤雨溪：Vue3 将不会支持 IE11 精力会投入到 Vue2.7》](https://juejin.cn/post/6946756821675671566 "https://juejin.cn/post/6946756821675671566")
*   [《Vue 超好玩的新特性：在 CSS 中引入 JS 变量》](https://juejin.cn/post/6856668819344392206 "https://juejin.cn/post/6856668819344392206")
*   [《什么？仅靠 H5 标签就能实现收拉效果？》](https://juejin.cn/post/6912374170743472135 "https://juejin.cn/post/6912374170743472135")
*   [《整治 GitHub 不文明现象！微软推出评论区！》](https://juejin.cn/post/6910828161030701064 "https://juejin.cn/post/6910828161030701064")
*   [《Vue 3.0.3 : 新增 CSS 变量传递以及最新的 Ref 提案》](https://juejin.cn/post/6899439012331651079 "https://juejin.cn/post/6899439012331651079")
*   [《双 11 小黑盒很炫酷？咱们用 CSS 变量来改进一下！》](https://juejin.im/post/6893875394584248334 "https://juejin.im/post/6893875394584248334")
*   [《千万别小瞧九宫格 一道题就能让候选人原形毕露！》](https://juejin.im/post/6886770985060532231 "https://juejin.im/post/6886770985060532231")
*   [《将原型对象设置成 Proxy 后的一系列迷惑行为》](https://juejin.im/post/6877430232987467789 "https://juejin.im/post/6877430232987467789")
*   [《Vue 超好玩的新特性：DOM 传送门》](https://juejin.im/post/6868260498417123335 "https://juejin.im/post/6868260498417123335")
*   [《终于轮到 Vue 来带给 React 灵感了？》](https://juejin.im/post/6844904015004696583 "https://juejin.im/post/6844904015004696583")
*   [《Vue3 在 IOS 下的一个小坑》](https://juejin.im/post/6844904145275584519 "https://juejin.im/post/6844904145275584519")
*   [《[译] 尤雨溪：Vue3 的设计过程》](https://juejin.im/post/6844903823937372174 "https://juejin.im/post/6844903823937372174")