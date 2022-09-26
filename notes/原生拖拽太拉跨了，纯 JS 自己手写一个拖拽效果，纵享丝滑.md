> 本文由 [简悦 SimpRead](http://ksria.com/simpread/) 转码， 原文地址 [juejin.cn](https://juejin.cn/post/7145447742515445791)

前言
--

提到元素拖拽，通常都会先想到用 **HTML5** 的拖拽放置 (Drag 和 Drop) 来实现，它提供了一套完整的事件机制，看起来似乎是首选的解决方案，但实际却不是那么美好，主要是它的样式太过简陋，无法实现更高级的用户体验：

![](https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/92ce43734d3c4ff7afeafe163b674983~tplv-k3u1fbpfcp-zoom-in-crop-mark:3024:0:0:0.awebp?)

> 其实这是浏览器默认的一种拖拽效果，随便拖拽任意的图片都会产生 (包括文字)：
> 
> ![](https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/f33051f97be34a4683d09808f19650a2~tplv-k3u1fbpfcp-zoom-in-crop-mark:3024:0:0:0.awebp?)

笔者因为之前有个小项目需要经常参考**稿定设计**，一直有留意其元素拖拽的效果 (如下图)，所以接下来我将以这种效果为蓝本，使用原生 JS 实现一个富有动感的 **自定义拖拽** 效果，话不多说直接开摸。

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/cf40358d4f384b7f9b3a1768e8b30843~tplv-k3u1fbpfcp-zoom-in-crop-mark:3024:0:0:0.awebp?)

实现原理
----

首先说下思路，我们需要知道鼠标的三个事件，分别是 `mousedown`，`mousemove`，`mouseup` ，当点击按下的时候，克隆一个绝对定位的元素，并标识下 "拖拽中" 的状态，接着在 `mousemove` 中就可以判断应该执行的具体方法，从而让元素随着鼠标移动起来。

在监听事件的 `event` 对象中，有几个参数是比较重要的：`clientX`，`clientY` 标识的鼠标当前横坐标和纵坐标，`offsetX` 和 `offsetY` 表示相对偏移量，可以在 `mousedown` 鼠标按下时记录初始坐标，在 `mouseup` 鼠标抬起时判断是否在目标区域中，如果是则用鼠标获取到的**当前的偏移量 - 初始坐标**得到元素实际在目标区域中的位置。

> 为方便阅读，以下所有代码均有部分省略，且演示 GIF 会掉帧，非常建议查看码上掘金阅读[完整源码](#heading-9 "#heading-9")配合本文食用，代码量并不多。

基础界面
----

先简单实现一个两栏布局界面，并应用上一些 CSS 效果:

```
<div id="app">
  <div class="slide">
    <div id="list">
      <img class="item" src="......." />
      <img  .........
    </div>
  </div>
  <div class="content"></div>
</div>
复制代码
```

```
#app {
  width: 100vw;
  height: 100vh;
  display: flex;
}
.active {
  cursor: grabbing;
}

.slide {
  width: 260px;
  height: 100%;
  overflow: scroll;
  border-right: 1px solid rgba(0,0,0,.15);
  #list {
    user-select: none;
    .item {
      background: rgba(0,0,0,.15);
      width: 120px;
      display: inline-block;
      break-inside: avoid;  
      margin-bottom: 4px;
    }
    .item:hover {
      cursor: grab;
      filter: brightness(90%);
    }
    .item:active {
      cursor: grabbing;
    }
  }
  .grid {
      column-count: 2;
      column-gap: 0px;
  }
}
.slide::-webkit-scrollbar {
  display: none; /* Chrome Safari */
}

#content {
  position: relative;
  flex: 1;
  height: 100%;
  margin-left: 45px;
  background: rgba(0,0,0,.07);
  .item {
    position: absolute;
    transform-origin: top left;
  }
}
复制代码
```

利用滤镜 `filter: brightness(90%);` 调节明亮度可以快速实现一个鼠标覆盖的动态效果，无需额外制作遮罩：

![](https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/cd3d8ff36c1e41539fc05a42bccd9a4b~tplv-k3u1fbpfcp-zoom-in-crop-mark:3024:0:0:0.awebp?)

使用伪类激活 `cursor` 的 `grab` 和 `grabbing` 可以设置抓取动作的图标:

![](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/f0ce1d39f6fd4d97a24c0ade810dcc39~tplv-k3u1fbpfcp-zoom-in-crop-mark:3024:0:0:0.awebp?)

实现元素抓取
------

利用[**事件委托机制**](https://juejin.cn/post/7137549438423138341#heading-7 "https://juejin.cn/post/7137549438423138341#heading-7")为选择列表添加 `mousedown` 事件监听，实现抓取的原理是在鼠标按下时**克隆**按下的元素，并把克隆出来的元素设置成绝对定位，让它 "浮" 起来：

```
let dragging = false
let cloneEl = null // 克隆元素
let initial = {} // 初始化数据记录
......
// 选中了元素
cloneEl = e.target.cloneNode(true) // 克隆元素
cloneEl.classList.add('flutter') // 使其浮动
e.target.parentElement.appendChild(cloneEl) // 加入到列表中
dragging = true // 标记拖动开始

// TODO: 初始化克隆元素的定位并记录，方便后面移动时计算位置
........
复制代码
```

```
.flutter {
  position: absolute;
  z-index: 9999;
  pointer-events: none;
}
复制代码
```

将鼠标的坐标设置为克隆元素的绝对定位值 (`left`、`top`)，就会像下图所示这样，此时减去 `offset` 偏移量，就能让克隆元素覆盖在本体上面。

![](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/914b136b3b4b43eb8d346ab4fb964cb0~tplv-k3u1fbpfcp-zoom-in-crop-mark:3024:0:0:0.awebp?)

初始化的值需要记录起来方便后续计算，同时我们用 `dragging` 变量标记了状态 (拖动中)，接下来配合移动鼠标的监听事件就能将元素“抓” 起来了：

```
// 鼠标移动
window.addEventListener("mousemove", (e) => {
  if (dragging && cloneEl) {
    // TODO: 处理元素的移动：改变 left top 定位
    // x 轴（left）计算方法：e.clientX - initial.offsetX
    // y 轴（top）计算方法：e.clientY - initial.offsetY
  }
})
复制代码
```

![](https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/963e2476f18d45d4b4db85478b5616a7~tplv-k3u1fbpfcp-zoom-in-crop-mark:3024:0:0:0.awebp?)

上面只是实现了元素的拖动，但是 " **克隆** " 的效果实在太明显了，为了让元素看起来更像是拖出来的而不是复制出来的，我们还要让本体隐藏，同时 DOM 结构不能丢失，这时只需在按下拖动时给本体元素设置个 `opacity: 0`，结束时再改回透明度 1 就能搞定。

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/4ea464f61b614856b6332a299245c26c~tplv-k3u1fbpfcp-zoom-in-crop-mark:3024:0:0:0.awebp?)

虽然到这功能就算实现了，但实际效果还是有点僵硬，参考稿定设计中的元素放开时会固定回到一个位置，然后再收回去，这个过渡又有点鬼畜，不够流畅。其实只需让元素回退过程有一个自然地动画就行，`transition` 就能实现：

```
.is_return {
  transition: all 0.3s;
}
复制代码
```

```
// 鼠标抬起
window.addEventListener("mouseup", (e) => {
  dragging = false
  if (cloneEl) {
      cloneEl.classList.add('is_return') // 加上过渡动画
      changeStyle(......) // 设置回元素的初始位置
      setTimeout(() => {
        cloneEl.remove() // 移除元素
      }, 300)
  }
})
复制代码
```

最终我在动作结束时给克隆元素添加了过渡属性，然后直接设置回初始坐标让克隆元素回到它的出生地点，用定时器在过渡动画持续的相同时间后移除克隆元素，这样就有了一个平滑稳定的回退动画。

![](https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/4e7ba4f0255e4f8aaf4243f09d0ab7bf~tplv-k3u1fbpfcp-zoom-in-crop-mark:3024:0:0:0.awebp?)

### 性能优化

由于在改变元素状态的过程中需要频繁进行多个 CSS 操作，为降低回流重绘的成本，最好将多个操作合并起来处理，这里利用了 `cssText` 来实现：

```
// 改变漂浮元素：x、y、缩放倍率
function moveFlutter(x, y, d = 0) {
  const scale = d ? initial.width + d < initial.fakeSize ? `transform: scale(${(initial.width + d) / initial.width})` : null : null
  const options = [`left: ${x}px`, `top: ${y}px`]
  scale && options.push(scale)
  // 将CSS处理成数组，然后丢进DOM操作方法中一次执行
  changeStyle(options)
}
// 合并多个操作
function changeStyle(arr) {
  const original = cloneEl.style.cssText.split(';')
  original.pop()
  cloneEl.style.cssText = original.concat(arr).join(';') + ';'
}
复制代码
```

实现拖拽放大
------

放大我们可以使用 `transform: scale` 来实现，只需要将拖动位置之间的距离当做**变化系数**（假设为`d`），那么`scale`变化数值即为`(元素宽度 + d)/元素宽度`，而放大的最终倍数必定为 `图片实际宽度/元素的宽度`，只要判断不超过这个边界就可以。（这个图片实际宽高在真实业务场景中建议在上传资源时就记录在数据库，这里我是模拟的随机一个原图尺寸）。

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/2bf1ec501c794249b7a1636a779ae04a~tplv-k3u1fbpfcp-zoom-in-crop-mark:3024:0:0:0.awebp?)

两点间距离计算公式为 (x1​−x2​)2+(y1​−y2​)2​，代码实现：

```
// 计算两点之间距离
function distance({ clientX, clientY }) {
  const { clientX: x, clientY: y } = initial // 获取初始的坐标
  const b = clientX - x;
  const a = clientY - y;
  return Math.sqrt(Math.pow(b, 2) + Math.pow(a, 2))
}

window.addEventListener("mousemove", (e) => {
  if (dragging && cloneEl) {
    const d = distance(e) // 计算距离
    moveFlutter(e.clientX - initial.offsetX, e.clientY - initial.offsetY, d)
  }
})
function moveFlutter(x, y, d = 0) {
  let scale = ''
  // 如果距离大于0，且宽度+距离小于实际宽度
  if( d && initial.width + d <= initial.fakeSize ) {
      scale = `transform: scale(${(initial.width + d) / initial.width})`
  }
  // TODO ... changeStyle ...
}
复制代码
```

效果演示，GIF 稍微掉帧：

![](https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/c4dbf823c2624ab59753bca320e4d11b~tplv-k3u1fbpfcp-zoom-in-crop-mark:3024:0:0:0.awebp?)

> 注意元素都要设置 `transform-origin: top left;` 改变缩放原点到左上角，否则默认 (中心为原点) 的转换会发生比较明显的偏移。

实现放置
----

其实拖拽放置有点像是 "复制" 与 "粘贴"，前面我们实现了**复制**，放置主要就是将元素**粘贴**到画布当中，流程步骤如下：

1.  如果鼠标在目标区域，拷贝元素到画布中，如果不在画布中，执行倒退动画
2.  删除元素

```
// 完成处理
function done(x, y) {
  if (!cloneEl) { return }
  const newEl = cloneEl.cloneNode(true)
  newEl.classList.remove('flutter')
  newEl.src = cloneEl.getAttribute('raw') // 设置原图地址
  newEl.style.cssText = `left: ${x - initial.offsetX}px; top: ${y - initial.offsetY}px;`
  document.getElementById('content').appendChild(newEl)
  // TODO: 元素移除
}
复制代码
```

判断是否在画布内抬起很简单，往画布上绑定`mouseup`监听事件即可，克隆的新元素必须删除无用的属性和`class`，此时设置元素的`left`、`top`即可将元素放置进画布中，关键点在于画布内的`target`有可能是错的，因为如果鼠标抬起的区域已经放置了元素，那么相对偏移量就得我们自己计算了，使用`getBoundingClientRect`方法获取画布本身相对于视窗的偏移，鼠标坐标减去画布本身的偏移就是元素在画布中的位置了。

![](https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/c4a53a0ad25b416c90a8f321e4d69d88~tplv-k3u1fbpfcp-zoom-in-crop-mark:3024:0:0:0.awebp?)

```
document.getElementById('content').addEventListener("mouseup", (e) => {
  if (e.target.id !== 'content') {
    const lostX = e.x - document.getElementById('content').getBoundingClientRect().left
    const lostY = e.y - document.getElementById('content').getBoundingClientRect().top
    done(lostX, lostY)
  } else { done(e.offsetX, e.offsetY) }
})
复制代码
```

只贴了部分关键代码，完整代码建议 [在码上掘金中查看](#heading-9 "#heading-9")。

边界判断
----

如果不对边界情况进行处理可能会导致拖动时发生意外的中断，无法正确回收克隆元素。

```
// 鼠标离开了视窗
document.addEventListener("mouseleave", (e) => {
  end()
})
// 用户可能离开了浏览器
window.onblur = () => {
  end()
}
复制代码
```

体验优化
----

参考稿定设计中元素拖拽是直接赋值原图的，原图大小通常无法控制，免不了需要加载时间，造成卡顿空白的问题，在网络不够快时体验尤其尴尬：

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/bc90a403f68641f68a0c0e2c1770e638~tplv-k3u1fbpfcp-zoom-in-crop-mark:3024:0:0:0.awebp?)

我的优化思路是利用浏览器加载过同一张图片就会优先读缓存的机制，先用一个`Image`加载原图，等其加载完毕再把拖拽元素的`src`改成原图，这样浏览器会 "自动" 帮我们优化这个过程，只需要注意一点，由于这是个异步任务，所以一定要做好对应标记，不然手速快的时候控制不好触发顺序。

```
function simulate(url, flag) {
  cloneEl.setAttribute('raw', url)
  const image = new Image()
  image.src = url
  image.onload = function () {
    // 异步任务，克隆节点可能已不存在，flag标记是否拖动的还是当前目标
    cloneEl && initial.flag === flag && (cloneEl.src = url)
  }
}
复制代码
```

效果演示，故意加大了图片的分辨率差异：

![](https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/ac9af3f26d6443a7bf0ce7e0480ec538~tplv-k3u1fbpfcp-zoom-in-crop-mark:3024:0:0:0.awebp?)

完整代码
----

往期文章推荐
------

[# 如何优雅地编写一个高逼格的 JS 插件惊艳你的领导和同事？](https://juejin.cn/post/7143941617395695623 "https://juejin.cn/post/7143941617395695623")

[# 给掘金写了个有趣又好玩的一键三连插件 | 仿 B 站效果](https://juejin.cn/post/7130089643256578078 "https://juejin.cn/post/7130089643256578078")

[# 这道 JS 经典面试题不要背！今天帮你彻底搞懂它](https://juejin.cn/post/7140901067327275044 "https://juejin.cn/post/7140901067327275044")

[# Vue 实现无限级树形选择器（无第三方依赖）](https://juejin.cn/post/7140586249001107470 "https://juejin.cn/post/7140586249001107470")