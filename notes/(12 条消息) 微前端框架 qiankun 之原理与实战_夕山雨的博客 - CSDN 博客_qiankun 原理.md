> 本文由 [简悦 SimpRead](http://ksria.com/simpread/) 转码， 原文地址 [blog.csdn.net](https://blog.csdn.net/qq_41694291/article/details/113842872?spm=1001.2101.3001.6650.12&utm_medium=distribute.pc_relevant.none-task-blog-2%7Edefault%7EBlogCommendFromBaidu%7ERate-12.pc_relevant_default&depth_1-utm_source=distribute.pc_relevant.none-task-blog-2%7Edefault%7EBlogCommendFromBaidu%7ERate-12.pc_relevant_default&utm_relevant_index=15)

### 目录

*   [一、微前端概述](#_1)
*   *   [1. 基本原理](#1__2)
    *   [2. 微前端的主要优势](#2__39)
    *   [3. 当前微前端方案的一些缺点](#3__49)
*   [二、qiankun 与 single-spa 实现原理](#qiankunsinglespa_56)
*   *   [1. single-spa 实现原理](#1_singlespa_71)
    *   *   [(1). 路由问题](#1__73)
        *   [(2). 应用入口](#2__160)
        *   [(3). 应用加载](#3__183)
    *   [2. qiankun 实现原理](#2_qiankun_237)
    *   *   [(1). 应用加载](#1__238)
        *   [(2). js 隔离](#2_js_322)
        *   [(3). css 隔离](#3_css_391)
        *   [(4). 应用通信](#4__429)
*   [三、qiankun 实战](#qiankun_459)
*   *   [1. 搭建基座应用](#1__467)
    *   [2. 搭建子应用](#2__567)
*   [总结](#_656)

一、微前端概述
=======

1. 基本原理
-------

在正式介绍 qiankun 之前，我们需要知道，它是基于另一个微前端[框架](https://so.csdn.net/so/search?q=%E6%A1%86%E6%9E%B6&spm=1001.2101.3001.7020)：single-spa 搭建的。qiankun 在它的基础上进行了封装和增强，使其更加易用。本文我们会先从 single-spa 入手，一步步介绍 qiankun 的实现原理。在讲解两者之前，我们先来了解一下何为微前端。

微前端的概念借鉴自后端的[微服务](https://so.csdn.net/so/search?q=%E5%BE%AE%E6%9C%8D%E5%8A%A1&spm=1001.2101.3001.7020)，主要是为了解决大型工程在变更、维护、扩展等方面的困难而提出的。目前主流的微前端方案包括以下几个：

1.  **iframe**
2.  **基座模式**，主要基于路由分发，`qiankun`和`single-spa`就是基于这种模式
3.  **组合式集成**，即单独构建组件，按需加载，类似 npm 包的形式
4.  **EMP**，主要基于`Webpack5 Module Federation`
5.  **Web Components**

严格来讲，这些方案都不算是完整的微前端解决方案，它们只是用于解决微前端中**运行时容器**的相关问题。除了运行时容器，一套完整的微前端方案还需要解决**版本管理、质量管控、配置下发、线上监控、灰度发布、安全监测**等与工程和平台相关的问题，而这些问题中的大部分工作目前仍处于探索阶段。  
![](https://img-blog.csdnimg.cn/20210222001907103.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQxNjk0Mjkx,size_16,color_FFFFFF,t_70)  
**iframe**：是传统的微前端解决方案，基于 iframe 标签实现，技术难度低，隔离性和兼容性很好，但是性能和使用体验比较差，多用于集成第三方系统；  
**基座模式**：主要基于路由分发，即由一个基座应用来监听路由，并按照路由规则来加载不同的应用，以实现应用间解耦；  
**组合式集成**：把组件单独打包和发布，然后在构建或运行时组合；  
**EMP**：基于`Webpack5 Module Federation`，一种去中心化的微前端实现方案，它不仅能很好地隔离应用，还可以轻松实现应用间的资源共享和通信；  
**Web Components**：是官方提出的组件化方案，它通过对组件进行更高程度的封装，来实现微前端，但是目前兼容性不够好，尚未普及。

总的来说，iframe 主要用于简单并且性能要求不高的第三方系统；组合式集成目前主要用于前端组件化，而不是微前端；**基座模式**、**EMP** 和 **Web Components** 是目前主流的微前端方案。

本文我们主要对`qiankun`所基于的基座模式进行介绍。它的**主要思路是将一个大型应用拆分成若干个更小、更简单，可以独立开发、测试和部署的子应用**，然后由一个基座应用根据路由进行应用切换。

如果以前端组件的概念作类比，我们可以把每个被拆分出的子应用看作是一个**应用级组件**，每个应用级组件专门实现某个特定的业务功能（如商品管理、订单管理等）。这里实际上谈到了微前端拆分的原则：即以业务功能为基本单元。经过拆分后，整个系统的结构也发生了变化：  
![](https://img-blog.csdnimg.cn/20210222143728755.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQxNjk0Mjkx,size_16,color_FFFFFF,t_70)

左侧是传统大型单页应用的前端架构，所有模块都在一个应用内，由应用本身负责路由管理，是**应用分发路由**的方式；而右侧是基座模式下的系统架构，各个子应用互不相关，单独运行在不同的服务上，由基座应用根据路由选择加载哪个应用到页面内，是**路由分发应用**的方式。这种方式使得各个模块的耦合性大大降低，而微前端需要解决的主要问题就是如何拆分和组织这些子应用。

为了让这些拆分出的子应用在一个单页面内协同工作，我们需要一个 “管理者” 应用，这就是我们上面说的基座应用，也叫主应用。基座应用一般是用户最终访问的应用，它会根据定义的规则，将不同的应用加载到页面内供用户使用。当然，这种架构下的每个子应用也具备单独访问的能力。

为了配合基座应用，子应用必须经过一些改造，向外暴露出相应的生命周期钩子，以便基座应用加载和卸载。实际上，一个典型的基于 vue-router 的 Vue 应用与这种架构存在着很大的相似性：  
![](https://img-blog.csdnimg.cn/20210222144320456.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQxNjk0Mjkx,size_16,color_FFFFFF,t_70)

在典型的 Vue 应用中，各个组件当然都必须基于 Vue 编写；但是**在微前端架构中，各个子应用可以基于不同的技术框架**，这也是它最大的优势之一。这是因为各个子应用是独立编译和部署的，而基座应用是在运行时动态加载的子应用，由于在启动子应用时已经经历过编译阶段，所以基座应用加载的都是原生 JavaScript 代码，自然与子应用所用的技术框架无关（`qiankun`甚至能加载 jQuery 编写的页面）。

概念性地讲，在微前端架构中，**各个子应用将一些特定的业务功能封装在一个业务黑箱中**，只对外暴露少量生命周期方法；**基座应用根据路由地址变化，动态地加载对应的业务黑箱，并将其渲染到指定的占位 DOM 元素上**。与 Vue 应用一样，微前端也可以一次加载多个业务黑箱，这称为多实例模式（类似于 vue-router 的命名视图）。

2. 微前端的主要优势
-----------

1.  技术兼容性好，各个子应用可以基于不同的技术架构
2.  代码库更小、内聚性更强
3.  便于独立编译、测试和部署，可靠性更高
4.  耦合性更低，各个团队可以独立开发，互不干扰
5.  可维护性和扩展性更好，便于局部升级和增量升级

关于**技术兼容性**，由于在被基座应用加载前， 所有子应用已经编译成原生代码输出，所以基座应用可以加载各类技术栈编写的应用；由于拆分后应用体积明显变小，并且每个应用只实现一个业务模块，因此其**内聚性更强**；另外子应用本身也是完整的应用，所以它可以**独立编译、测试和部署**；关于**耦合性**，由于各个子应用只负责各自的业务模块，所以耦合性很低，非常便于独立开发；关于**可维护性和扩展性**，由于拆分出的应用都是完整的应用，因此专门升级某个功能模块就成为了可能，并且当需要增加模块时，只需要创建一个新应用，并修改基座应用的路由规则即可。

不过这种微前端方案仍然存在缺点：

3. 当前微前端方案的一些缺点
---------------

1.  子应用间的资源共享能力较差，使得项目总体积变大
2.  需要对现有代码进行改造（指的是未按照微前端形式编写的旧工程）

首先，子应用之间保持较高的独立性，反而使一些**公共资源不便于共享**。虽然大型第三方库可以通过 externals 的方式上传到 cdn，但像一些工具函数，通用业务组件等则不易共享，这就使得项目整体体积反而变大。由于改造成本不高，**代码改造**通常算不上很严重的问题，但仍存在一定的代价。

介绍完微前端的基本概念，我们就来看一下`qiankun`和`single-spa`的核心实现原理。

二、qiankun 与 single-spa 实现原理
===========================

既然`qiankun`是基于`single-spa`的，那么我们就来看`qiankun`和`single-spa`在架构中分别扮演了什么角色。

一般来说，微前端需要解决的问题分为两大类：

1.  **应用的加载与切换**
2.  **应用的隔离与通信**

应用的加载与切换需要解决的问题包括：**路由问题、应用入口、应用加载**；应用的隔离与通信需要解决的问题包括：**js 隔离、css 样式隔离、应用间通信**。

`single-spa`很好地解决了**路由**和**应用入口**两个问题，但并没有解决应用加载问题，而是将该问题暴露出来由使用者实现（一般可以用`system.js`或原生`script`标签来实现）；`qiankun`在此基础上封装了一个**应用加载**方案（即`import-html-entry`），并给出了 **js 隔离**、**css 样式隔离**和**应用间通信**三个问题的解决方案，同时提供了**预加载功能**。

借助`single-spa`提供的能力，我们只能把不同的应用加载到一个页面内，但是很难保证这些应用不会互相干扰。而`qiankun`为我们解决了这些后顾之忧，使得它成为一个更加完整的微前端运行时容器。  
![](https://img-blog.csdnimg.cn/20210219154623177.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQxNjk0Mjkx,size_16,color_FFFFFF,t_70)  
接下来我们借助部分源码，分别来看`single-spa`和`qiankun`是如何一步步实现运行时容器的。

1. single-spa 实现原理
------------------

我们已经知道，`single-spa`解决的是应用的加载与切换相关的问题，下面就来看完整的实现过程。

### (1). 路由问题

`single-spa`是通过监听 **hashChange** 和 **popState** 这两个原生事件来检测路由变化的，它会根据路由的变化来加载对应的应用，相关的代码可以在`single-spa`的 `src/navigation/navigation-events.js` 中找到：

```
...
// 139行
if (isInBrowser) {
  // We will trigger an app change for any routing events.
  window.addEventListener("hashchange", urlReroute);
  window.addEventListener("popstate", urlReroute);
...
// 174行，劫持pushState和replaceState
  window.history.pushState = patchedUpdateState(
    window.history.pushState,
    "pushState"
  );
  window.history.replaceState = patchedUpdateState(
    window.history.replaceState,
    "replaceState"
  );
```

我们看到，`single-spa`在检测到发生`hashChange`或`popState`事件时，会执行`urlReroute`函数，这里封装了它对路由问题的解决方案。另外，它还劫持了原生的`pushState`和`replaceState`事件，关于为什么劫持这两个事件，我们后面会介绍，我们先来看`urlReroute`函数做了什么：

```
function urlReroute() {
  reroute([], arguments);
}
```

这个函数只是调用了`reroute`函数，而`reroute`函数就是`single-spa`解决路由问题的核心逻辑，下面我们来分析一下它的实现，由于该函数较长，我们截取其中体现核心思路的代码进行分析：  
**src/navigation/reroute.js**

```
export function reroute(pendingPromises = [], eventArguments) {
  ...
  // getAppChanges会根据路由改变应用的状态，状态包含4类
  // 待清除、待卸载、待加载、待挂载
  const {
    appsToUnload,
    appsToUnmount,
    appsToLoad,
    appsToMount,
  } = getAppChanges();
  ...
  // 如果应用已启动，则调用performAppChanges加载和挂载应用
  // 否则，只加载未加载的应用
  if (isStarted()) {
    appChangeUnderway = true;
    appsThatChanged = appsToUnload.concat(
      appsToLoad,
      appsToUnmount,
      appsToMount
    );
    return performAppChanges();
  } else {
    appsThatChanged = appsToLoad;
    return loadApps();
  }
  ...
  function performAppChanges() {
    return Promise.resolve().then(() => {
      // 1. 派发应用更新前的自定义事件
      // 2. 执行应用暴露出的生命周期函数
      // appsToUnload -> unload生命周期钩子
      // appsToLoad -> 执行加载方法
      // appsToUnmount -> 卸载应用，并执行对应生命周期钩子
      // appsToMount -> 尝试引导和挂载应用
    })
  }
  ...
}
```

这里就是`single-spa`解决路由问题的主要逻辑。主要是以下几步：

1.  根据传入的参数`activeWhen`判断哪个应用需要加载，哪个应用需要卸载或清除，并将其 push 到对应的数组
2.  如果应用已经启动，则进行应用加载或切换。针对应用的不同状态，直接执行应用自身暴露出的生命周期钩子函数即可。
3.  如果应用未启动，则只去下载`appsToLoad`中的应用。

总的来看，当路由发生变化时，`hashChange`或`popState`会触发，这时`single-spa`会监听到，并触发`urlReroute`；接着它会调用`reroute`，该函数正确设置各个应用的状态后，直接通过调用应用所暴露出的生命周期钩子函数即可。当某个应用被推送到`appsToMount`后，它的`mount`函数会被调用，该应用就会被挂载；而推送到`appsToUnmount`中的应用则会调用其`unmount`钩子进行卸载。  
![](https://img-blog.csdnimg.cn/20210222005822932.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQxNjk0Mjkx,size_16,color_FFFFFF,t_70)  
上面我们还提到，`single-spa`除了监听`hashChange`或`popState`两个事件外，还劫持了原生的`pushState`和 `replaceState`两个方法，这是为什么呢？

这是因为像 **scroll-restorer** 这样的第三方组件可能会在页面滚动时，通过调用`pushState`或`replaceState`，将滚动位置记录在 state 中，而页面的 url 实际上没有变化。这种情况下，`single-spa`理论上不应该去重新加载应用，但是由于这种行为会触发页面的`hashChange`事件，所以根据上面的逻辑，`single-spa`会发生意外重载。

为了解决这个问题，`single-spa`允许开发者手动设置是否只对 url 值的变化监听，而不是只要发生`hashChange`或`popState`就去重新加载应用，我们可以像下面一样在启动`single-spa`时添加`urlRerouteOnly`参数：

```
singleSpa.start({
  urlRerouteOnly: true,
});
```

这样除非 url 发生了变化，否则`pushState`和`popState`不会导致应用重载。

### (2). 应用入口

`single-spa`采用的是**协议入口**，即只要实现了`single-spa`的入口协议规范，它就是可加载的应用。`single-spa`的规范要求应用入口必须暴露出以下三个生命周期钩子函数，且必须返回 Promise，以保证`single-spa`可以注册回调函数：

1.  bootstrap
2.  mount
3.  unmount  
    ![](https://img-blog.csdnimg.cn/20210222010751901.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQxNjk0Mjkx,size_16,color_FFFFFF,t_70)

**bootstrap** 用于应用引导，基座应用会在子应用挂载前调用它。举个应用场景，假如某个子应用要挂载到基座应用内`id`为`app`的节点上：

```
new Vue({
  el: '#app',
  ...
})
```

但是基座应用中当前没有`id`为`app`的节点，我们就可以在子应用的`bootstrap`钩子内手动创建这样一个节点并插入到基座应用，子应用就可以正常挂载了。所以它的作用就是做一些挂载前的准备工作。

**mount** 用于应用挂载，就是一般应用中用于渲染的逻辑，即上述的`new Vue`语句。我们通常会把它封装到一个函数里，在`mount`钩子函数中调用。

**unmount** 用于应用卸载，我们可以在这里调用实例的`destroy`方法手动卸载应用，或清除某些内存占用等。

除了以上三个必须实现的钩子外，`single-spa`还支持非必须的`load`、`unload`、`update`等，分别用于加载、卸载和更新应用。

那么只使用`single-spa`如何进行子应用加载呢？

### (3). 应用加载

实际上`single-spa`并没有提供自己的解决方案，而是将它开放出来，由开发者提供。

我们看一下基于`system.js`如何启动`single-spa`：

```
<script type="systemjs-importmap">
  {
    "imports": {
      "app1": "http://localhost:8080/app1.js",
      "app2": "http://localhost:8081/app2.js",
      "single-spa": "https://cdnjs.cloudflare.com/ajax/libs/single-spa/4.3.7/system/single-spa.min.js"
    }
  }
</script>
... // system.js的相关依赖文件
<script>
(function(){
  // 加载single-spa
  System.import('single-spa').then((res)=>{
    var singleSpa = res;
    // 注册子应用
    singleSpa.registerApplication('app1',
      () => System.import('app1'),
      location => location.hash.startsWith(`#/app1`);
    );
    singleSpa.registerApplication('app2',
      () => System.import('app2'),
      location => location.hash.startsWith(`#/app2`);
    );
    // 启动single-spa
    singleSpa.start();
  })
})()
</script>
```

我们在调用`singleSpa.registerApplication`注册应用时提供的第二个参数就是加载这个子应用的方法。如果需要加载多个 js，可以使用多个`System.import`连续导入。`single-spa`会调用这个函数，下载子应用代码并分别调用其`bootstrap`和`mount`方法进行引导和挂载。

从这里我们也可以看到`single-spa`的弊端。首先我们必须手动实现应用加载逻辑，挨个罗列子应用需要加载的资源，这在大型项目里是十分困难的（特别是使用了文件名 hash 时）；另外它只能以 js 文件为入口，无法直接以 html 为入口，这使得嵌入子应用变得很困难，也正因此，`single-spa`不能直接加载 jQuery 应用。

`single-spa`的`start`方法也很简单：

```
export function start(opts) {
  started = true;
  if (opts && opts.urlRerouteOnly) {
    setUrlRerouteOnly(opts.urlRerouteOnly);
  }
  if (isInBrowser) {
    reroute();
  }
}
```

先是设置`started`状态，然后设置我们上面说到的`urlRerouteOnly`属性，接着调用`reroute`，开始首次加载子应用。加载完第一个应用后，`single-spa`就时刻等待着`hashChange`或`popState`事件的触发，并执行应用的切换。

以上就是`single-spa`的核心原理，从上面的介绍中不难看出，`single-spa`只是负责把应用加载到一个页面中，至于应用能否协同工作，是很难保证的。而`qiankun`所要解决的，就是协同工作的问题。

2. qiankun 实现原理
---------------

### (1). 应用加载

上面我们说到了，`single-spa`提供的应用加载方案是开放式的。针对上面我们谈到的几个弊端，`qiankun`进行了一次封装，给出了一个更完整的应用加载方案，`qiankun`的作者将其封装成了 npm 插件`import-html-entry`。

该方案的主要思路是允许以 html 文件为应用入口，然后通过一个 html 解析器从文件中提取 js 和 css 依赖，并通过 fetch 下载依赖，于是在`qiankun`中你可以这样配置入口：

```
const MicroApps = [{
  name: 'app1',
  entry: 'http://localhost:8080',
  container: '#app',
  activeRule: '/app1'
}]
```

`qiankun`会通过`import-html-entry`请求`http://localhost:8080`，得到对应的 html 文件，解析内部的所有`script`和`style`标签，依次下载和执行它们，这使得应用加载变得更易用。我们看一下这具体是怎么实现的。

`import-html-entry`暴露出的核心接口是`importHTML`，用于加载 html 文件，它支持两个参数：

1.  **url**，要加载的文件地址，一般是服务中 html 的地址
2.  **opts**，配置参数

url 不必多说。opts 如果是一个函数，则会替换默认的 fetch 作为下载文件的方法，此时其返回值应当是 Promise；如果是一个对象，那么它最多支持四个属性：`fetch`、`getPublicPath`、`getDomain`、`getTemplate`，用于替换默认的方法，这里暂不详述。

我们截取该函数的主要逻辑：

```
export default function importHTML(url, opts = {}) {
  ...
  // 如果已经加载过，则从缓存返回，否则fetch回来并保存到缓存中
  return embedHTMLCache[url] || (embedHTMLCache[url] = fetch(url)
		.then(response => readResAsString(response, autoDecodeResponse))
		.then(html => {
		  // 对html字符串进行初步处理
		  const { template, scripts, entry, styles } = 
		    processTpl(getTemplate(html), assetPublicPath);
		  // 先将外部样式处理成内联样式
		  // 然后返回几个核心的脚本及样式处理方法
		  return getEmbedHTML(template, styles, { fetch }).then(embedHTML => ({
				template: embedHTML,
				assetPublicPath,
				getExternalScripts: () => getExternalScripts(scripts, fetch),
				getExternalStyleSheets: () => getExternalStyleSheets(styles, fetch),
				execScripts: (proxy, strictGlobal, execScriptsHooks = {}) => {
					if (!scripts.length) {
						return Promise.resolve();
					}
					return execScripts(entry, scripts, proxy, {
						fetch,
						strictGlobal,
						beforeExec: execScriptsHooks.beforeExec,
						afterExec: execScriptsHooks.afterExec,
					});
				},
			}));
		});
}
```

省略的部分主要是一些参数预处理，我们从 return 语句开始看，具体过程如下：

1.  检查是否有缓存，如果有，直接从缓存中返回
2.  如果没有，则通过 fetch 下载，并字符串化
3.  调用`processTpl`进行一次模板解析，主要任务是扫描出外联脚本和外联样式，保存在`scripts`和`styles`中
4.  调用`getEmbedHTML`，将外联样式下载下来，并替换到模板内，使其变成内部样式
5.  返回一个对象，该对象包含处理后的模板，以及`getExternalScripts`、`getExternalStyleSheets`、`execScripts`等几个核心方法  
    ![](https://img-blog.csdnimg.cn/20210222012610595.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQxNjk0Mjkx,size_16,color_FFFFFF,t_70)

`processTpl`主要基于正则表达式对模板字符串进行解析，这里不进行详述。我们来看`getExternalScripts`、`getExternalStyleSheets`、`execScripts`这三个方法：  
**getExternalStyleSheets**

```
export function getExternalStyleSheets(styles, fetch = defaultFetch) {
  return Promise.all(styles.map(styleLink => {
	if (isInlineCode(styleLink)) {
	  // if it is inline style
	  return getInlineCode(styleLink);
	} else {
	  // external styles
	  return styleCache[styleLink] ||
	  (styleCache[styleLink] = fetch(styleLink).then(response => response.text()));
	}
  ));
}
```

遍历 styles 数组，如果是内联样式，则直接返回；否则判断缓存中是否存在，如果没有，则通过 fetch 去下载，并进行缓存。

**getExternalScripts** 与上述过程类似。

**execScripts** 是实现 js 隔离的核心方法，我们放在下一部分 js 隔离里讲解。

通过调用`importHTML`方法，`qiankun`可以直接加载 html 文件，同时将外联样式处理成内部样式表，并且解析出 JavaScript 依赖。更重要的是，它获得了一个可以在隔离环境下执行应用脚本的方法`execScripts`。

### (2). js 隔离

上面我们说到，`qiankun`通过`import-html-entry`，可以对 html 入口进行解析，并获得一个可以执行脚本的方法`execScripts`。`qiankun`引入该接口后，首先为该应用生成一个 window 的代理对象，然后将代理对象作为参数传入接口，以保证应用内的 js 不会对全局`window`造成影响。由于 IE11 不支持 proxy，所以`qiankun`通过快照策略来隔离 js，缺点是无法支持多实例场景。

我们先来看基于`proxy`的 js 隔离是如何实现的。首先看`import-html-entry`暴露出的接口，照例我们只截取核心代码:  
**execScripts**

```
export function execScripts(entry, scripts, proxy = window, opts = {}) {
  ... // 初始化参数
  return getExternalScripts(scripts, fetch, error)
	.then(scriptsText => {
	  // 在proxy对象下执行脚本的方法
	  const geval = (scriptSrc, inlineScript) => {
	    const rawCode = beforeExec(inlineScript, scriptSrc) || inlineScript;
	    const code = getExecutableScript(scriptSrc, rawCode, proxy, strictGlobal);
        (0, eval)(code);
        afterExec(inlineScript, scriptSrc);
	  };
	  // 执行单个脚本的方法
      function exec (scriptSrc, inlineScript, resolve) { ... }
      // 排期函数，负责逐个执行脚本
      function schedule(i, resolvePromise) { ... }
      // 启动排期函数，执行脚本
      return new Promise(resolve => schedule(0, success || resolve));
    });
});
```

这个函数的关键是定义了三个函数：`geval`、`exec`、`schedule`，其中实现 js 隔离的是`geval`函数内调用的`getExecutableScript`函数。我们看到，在调这个函数时，我们把外部传入的`proxy`作为参数传入了进去，而它返回的是一串新的脚本字符串，这段新的字符串内的`window`已经被`proxy`替代，具体实现逻辑如下：

```
function getExecutableScript(scriptSrc, scriptText, proxy, strictGlobal) {
	const sourceUrl = isInlineCode(scriptSrc) ? '' : `//# sourceURL=${scriptSrc}\n`;

	// 通过这种方式获取全局 window，因为 script 也是在全局作用域下运行的，所以我们通过 window.proxy 绑定时也必须确保绑定到全局 window 上
	// 否则在嵌套场景下， window.proxy 设置的是内层应用的 window，而代码其实是在全局作用域运行的，会导致闭包里的 window.proxy 取的是最外层的微应用的 proxy
	const globalWindow = (0, eval)('window');
	globalWindow.proxy = proxy;
	// TODO 通过 strictGlobal 方式切换切换 with 闭包，待 with 方式坑趟平后再合并
	return strictGlobal
		? `;(function(window, self, globalThis){with(window){;${scriptText}\n${sourceUrl}}}).bind(window.proxy)(window.proxy, window.proxy, window.proxy);`
		: `;(function(window, self, globalThis){;${scriptText}\n${sourceUrl}}).bind(window.proxy)(window.proxy, window.proxy, window.proxy);`;
}
```

![](https://img-blog.csdnimg.cn/202102211816117.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQxNjk0Mjkx,size_16,color_FFFFFF,t_70)  
核心代码就是由两个矩形框起来的部分，它把解析出的`scriptText`（即脚本字符串）用`with(window){}`包裹起来，然后把`window.proxy`作为函数的第一个参数传进来，所以`with`语法内的`window`实际上是`window.proxy`。

这样，当在执行这段代码时，所有类似`var name = '张三'`这样的语句添加的全局变量`name`，实际上是被挂载到了`window.proxy`上，而不是真正的全局`window`上。当应用被卸载时，对应的`proxy`会被清除，因此不会导致 js 污染。而当你配置`webpack`的打包类型为`lib`时，你得到的接口大概如下：

```
var jquery = (function(){})();
```

如果你的应用内使用了 jquery，那么这个 jquery 对象就会被挂载到`window.proxy`上。不过如果你在代码内直接写`window.name = '张三'`来生成全局变量，那么`qiankun`就无法隔离 js 污染了。

`import-html-entry`实现了上述能力后，`qiankun`要做的就很简单了，只需要在加载一个应用时为其初始化一个`proxy`传递进来即可：  
**proxySandbox.ts**

```
export default class ProxySandbox implements SandBox {
  ...
  constructor(name: string) {
    ...
    const proxy = new Proxy(fakeWindow, {
      set () { ... },
      get () { ... }
    }
  }
}
```

每次加载一个应用，`qiankun`就初始化这样一个`proxySandbox`，传入上述`execScripts`函数中。

在 IE 下，由于`proxy`不被支持，并且没有可用的`polyfill`，所以`qiankun`退而求其次，采用快照策略实现 js 隔离。它的大致思路是，在加载应用前，将`window`上的所有属性保存起来（即拍摄快照）；等应用被卸载时，再恢复`window`上的所有属性，这样也可以防止全局污染。但是当页面同时存在多个应用实例时，`qiankun`无法将其隔离开，所以 IE 下的快照策略无法支持多实例模式。

关于快照模式我们就不详细介绍了，接下来看一下`qiankun`如何实现 css 样式隔离。

### (3). css 隔离

目前`qiankun`主要提供了两种样式隔离方案，一种是基于`shadowDom`的；另一种则是实验性的，思路类似于 Vue 中的`scoped`属性，给每个子应用的根节点添加一个特殊属性，用作对所有 css 选择器的约束。

开启样式隔离的语法如下：

```
registerMicroApps({
  name: 'app1',
  ...
  sandbox: {
    strictStyleIsolation: true
    // 实验性方案，scoped方式
    // experimentalStyleIsolation: true
  },
})
```

当启用`strictStyleIsolation`时，`qiankun`将采用`shadowDom`的方式进行样式隔离，即为子应用的根节点创建一个`shadow root`。最终整个应用的所有 DOM 将形成一棵`shadow tree`。我们知道，`shadowDom`的特点是，它内部所有节点的样式对树外面的节点无效，因此自然就实现了样式隔离。

但是这种方案是存在缺陷的。因为某些 UI 框架可能会生成一些弹出框直接挂载到`document.body`下，此时由于脱离了`shadow tree`，所以它的样式仍然会对全局造成污染。

此外`qiankun`也在探索类似于`scoped`属性的样式隔离方案，可以通过`experimentalStyleIsolation`来开启。这种方案的策略是为子应用的根节点添加一个特定的随机属性，如：

```
<div
  data-qiankun-asiw732sde
  id="__qiankun_microapp_wrapper__"
  data-
>
```

然后为所有样式前面都加上这样的约束：

```
.app-main { 
  字体大小：14 px ; 
}
// ->
div[data-qiankun-asiw732sde] .app-main {  
  字体大小：14 px ; 
}
```

经过上述替换，这个样式就只能在当前子应用内生效了。虽然该方案已经提出很久了，但仍然是实验性的，因为它不支持 @ keyframes，@ font-face，@ import，@ page（即不会被重写）。

### (4). 应用通信

一般来说，微前端中各个应用之前的通信应该是尽量少的，而这依赖于应用的合理拆分。反过来说，如果你发现两个应用间存在极其频繁的通信，那么一般是拆分不合理造成的，这时往往需要将它们合并成一个应用。

当然了，应用间存在少量的通信是难免的。`qiankun`官方提供了一个简要的方案，思路是基于一个全局的`globalState`对象。这个对象由基座应用负责创建，内部包含一组用于通信的变量，以及两个分别用于修改变量值和监听变量变化的方法：`setGlobalState`和`onGlobalStateChange`。

以下代码用于在基座应用中初始化它：

```
import { initGlobalState, MicroAppStateActions } from 'qiankun';

const initialState = {};
const actions: MicroAppStateActions = initGlobalState(initialState);

export default actions;
```

这里的`actions`对象就是我们说的`globalState`，即全局状态。基座应用可以在加载子应用时通过`props`将`actions`传递到子应用内，而子应用通过以下语句即可监听全局状态变化：

```
actions.onGlobalStateChange (globalState, oldGlobalState) {
  ...
}
```

同样的，子应用也可以修改全局状态：

```
actions.setGlobalState(...);
```

![](https://img-blog.csdnimg.cn/20210222014614441.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQxNjk0Mjkx,size_16,color_FFFFFF,t_70)

此外，基座应用和其他子应用也可以进行这两个操作，从而实现对全局状态的共享，这样各个应用之间就可以通信了。这种方案与 Redux 和 Vuex 都有相似之处，只是由于微前端中的通信问题较为简单，所以官方只提供了这样一个精简方案。关于其实现原理这里不再赘述，感兴趣的可以去看一下源码。

关于`qiankun`的核心原理到这里就介绍完了，下面我们看一下如果使用`qankun`搭建一个微前端项目。

三、qiankun 实战
============

我们上面说到，`qiankun`是基于基座模式的，所以它必然有一个基座应用（主应用），来管理各个子应用的加载和卸载。我们以一个基于 Vue 的管理系统为例，来看如何搭建一个微前端项目。

目前主流的管理系统大都是基于以下页面结构：  
![](https://img-blog.csdnimg.cn/20210221194604511.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQxNjk0Mjkx,size_16,color_FFFFFF,t_70)  
左侧`menu`是菜单区，点击不同的菜单会在`content`内容区域加载不同的菜单页，而顶部的`header`一般是一些用户信息，右侧的`help`一般是帮助区域。

一般来说，微前端的子应用不宜拆分得过细，应该以业务领域进行大致拆分，所以我们将`menu`、`header`和`help`这三个区域放在基座应用中实现；然后我们将每个一级菜单用一个微应用来实现。这样，当我们打开不同一级菜单下的页面时，就会发生应用切换；而在同一个一级菜单下进行选择时，不会切换应用。

1. 搭建基座应用
---------

有了思路之后，我们就可以开始搭建基座应用了。首先我们需要先搭建好一个常规的 Vue 应用，并编写好以上三个区域对应的组件，此时的根组件结构大概如下：

```
<template>
  <div class="container">
    <div class="app-header">
      <prime-header></prime-header>
    </div>
    <div class="app-menu">
      <prime-menu></prime-menu>
    </div>
    <div class="app-content">
      <div id="root-view"></div>
    </div>
    <div class="help-taggle">
      <prime-help></prime-help>
    </div>
  </div>
</template>
```

我们在内容区域写了这样一个标签`<div></div>`，它就像`vue-router`中的占位组件`<router-view>`一样，用于后续加载子应用。

除了内容区域外，我们应该已经搭建好了一个完整的 Vue 应用。现在我们需要对它进行改造，使得它成为一个基座应用。

首先，我们需要安装`qiankun`：

```
yarn add qiankun
```

然后我们创建一个文件，来定义各个子应用的入口：  
**micro-app.js**

```
const microApps: Array<any> = [{
  name: 'module-app1',
  entry: 'https://app1.example.com',
  activeRule: '/app1',
  container: '#root-view',
  sandbox: {
    strictStyleIsolation: true // 开启样式隔离
  }
}, {
  name: 'module-shop',
  entry: 'https://app2.example.com',
  activeRule: '/app2',
  container: '#root-view',
  sandbox: {
    strictStyleIsolation: true // 开启样式隔离
  }
}, ...
];

export default microApps;
```

需要注意的是，这里所有子应用的`name`都不能重复，因为`qiankun`需要基于它进行缓存。

`entry`属性是定义子应用入口，基于`qiankun`的应用一般直接写子应用的入口 html 地址即可。

`activeRule`则是定义何时加载该子应用，该参数最终会被映射成为`single-spa`中的`activeWhen`参数，用于匹配 url。它们的格式也是一样的，可以是字符串，或正则表达式，或是一个返回 boolean 值的函数。这里子应用 1 的`activeRule`定义为`/app1`，就意味着当 url 的 hash 部分是以`/app1`开头时，基座应用就需要去加载应用`app1`了。一般来说我们可能更倾向于使用 history 模式，此时它直接匹配的就是域名后面的字符串。要启用 history 模式，只需要进行如下配置：

```
const router = new VueRouter({
  mode: 'history',
  routes
});
```

`container`是定义子应用的加载位置，还记得我们之前留出的 id 为`router-view`的标签吗，这里我们就是指定它为加载位置，整个子应用会加载为它的子树。

`sandbox`我们上面已经提到了，用于设置是否启用沙箱模式，默认为 true。这里我们将其设置为对象，可以同时开启子应用的样式隔离。

有了这个数组，我们接着来看如何使用它。

实际上这只需要两步，第一是注册所有的微应用，第二是启动`qiankun`，这两步都有对应的 api。我们现在希望在加载左侧菜单栏的时候去注册和启动`qiankun`，于是我们可以在对应的组件内这样写：  
**menu.vue**

```
<script>
// 引入qiankun注册子应用和启动的接口函数
import { registerMicroApps, start } from 'qiankun';
// 引入微应用入口配置
import microApps from './modules/micro-apps';
@Component
export default class PrimeMenu extends Vue {
  async created () {
    registerMicroApps(microApps, {
      // 注册一些全局生命周期钩子，如进行日志打印，如果不需要可以不传
      beforeMount () {},
    });
    // 启动qiankun，并开启预加载
    start({ prefetch: true });
  }
}
</script>
```

到了这里其实基座应用已经搭建好了。以 history 模式为例，根据`qiankun`和`single-spa`的实现原理，当我们点击菜单的时候，我们可能会通过以下两种方式修改地址栏的地址：

```
window.history.pushState({}, '', '/app1/overview');
// 或者这样
window.location.href = '/app1/overview';
```

这两种方式都会触发`hashChange`事件，而`qiankun`借助`single-spa`所绑定的监听事件，会侦测到地址变化，从而执行`single-spa`的`reroute`函数；随后`qiankun`通过`import-html-entry`提供的能力，去下载`app1`对应的 html 文件，并从中解析出所依赖的脚本和样式文件。`qiankun`会将其封装在一个沙箱中，执行这些脚本并添加样式表，从而渲染出子应用。经过这些步骤，应用`app1`就会像一个原生的组件一样被渲染到基座应用的占位节点内。

当点击其他菜单时，同样会触发`hashChange`事件，`single-spa`会重新执行`reroute`，卸载原子应用，加载新的子应用。

2. 搭建子应用
--------

除了需要处理基座应用，子应用也需要进行一定的改造。

假设我们现在已经启动了一个完整的概况页应用，地址为`https://app1.example.com`。我们现在希望这个页面可以像原生组件一样直接渲染到基座应用的`content`内容区域，那么按照`single-spa`的应用入口协议，我们必须在子应用内暴露出三个必要的生命周期钩子函数：  
**main.js**

```
...
let instance = null;
// 定义渲染函数
function render (props: any = {}) {
  instance = new Vue({
    router: router(props),
    store,
    render: h => h(App)
  }).$mount(`#app`);
}
// bootstrap引导函数
export async function bootstrap () {
  console.log('[vue] vue app bootstraped');
}
// 挂载函数
export async function mount (props: any) {
  console.log('[vue] props from main framework', props);
  // storeTest(props);
  render(props);
}
// 卸载函数
export async function unmount () {
  instance.$destroy();
  instance.$el.innerHTML = '';
  instance = null;
}
// 在qiankun环境下，修正加载路径
if (window.__POWERED_BY_QIANKUN__) {
  // eslint-disable-next-line no-undef
  __webpack_public_path__ = window.__INJECTED_PUBLIC_PATH_BY_QIANKUN__;
}
// 在非qiankun环境下，直接执行渲染
if (!window.__POWERED_BY_QIANKUN__) {
  render();
}
```

我们看到，不同于之前的写法，这里使用`export`向外暴露出了三个生命周期函数。

给`__webpack_public_path__`赋值的语句是为了防止子应用资源路径异常，由`qiankun`官方推荐设置。

最后面的判断语句，检查了`window.__POWERED_BY_QIANKUN__`是否存在，这是为了检查当前子应用是否正在被`qiankun`框架所加载；如果不是被`qiankun`加载的，那么直接执行渲染即可。

现在的子应用已经符合了`single-spa`入口协议规范，理论上它可以被`single-spa`所加载，但这还不够。因为按照正常的打包规则，应用最终会被打包成为一个匿名的立即执行函数，`single-spa`仍然无法从中解析出这些生命周期钩子，所以接下来我们需要修改`webpack`配置，让它以`lib`的格式打包：  
**vue.config.js**

```
module.exports = {
  ...
  configureWebpack: {
    output: {
      // 把子应用打包成 umd 库格式
      library: `micro-app1-[name]`,
      libraryTarget: 'umd',
      jsonpFunction: `webpackJsonp_micro-app1`
    }
  },
  // 以下配置可以修复一些字体文件加载路径问题
  chainWebpack: config => {
    config
      .plugin('html')
      .tap(args => {
        args[0].name = name;
        return args
      });

    config.module
      .rule("fonts")
      .test(/.(ttf|otf|eot|woff|woff2)$/)
      .use("url-loader")
      .loader("url-loader")
      .tap(options => {
        options = {
          // limit: 10000,
          name: '/static/fonts/[name].[ext]',
        }
        return options
      })
      .end();
  },
}
```

`umd`格式的应用会向外暴露指定的生命周期钩子函数，便于`single-spa`解析。`jsonpFunction`配置是`webpack`打包之后保存在 window 中的 key，只需保证各个子应用不一致即可。其余配置主要是解决一些加载路径问题。

完成以上配置后，分别启动基座应用和子应用。当访问基座应用并点击概况菜单时，地址栏的 url 会发生变化，`qiankun`就会自动去子应用所在的服务下载 html 文件，并解析出脚本和样式进行渲染，于是这个子应用就会展示在图中的`content`内容区域了。至此，一个简单的`qiankun`应用就搭建完毕了。

总结
==

`qiankun`和`single-spa`的原理算不上特别复杂，使用起来也很方便。并且`qiankun`还支持加载 jQuery 技术栈的页面，对旧系统可以说是非常友好的。

从目前微前端的发展来看，webpack5 的`Module Federation`可能会成为一个焦点功能，用于解决微前端应用间的资源共享问题（EMP 已经做到了这一点，`qiankun`的维护者也表示会集成这一功能）。当然了，随着`Web Components`功能的不断增强，也许浏览器本身就是微前端最完美的运行时容器。除了运行时容器，本文开篇提到的版本管理、质量管控、配置下发、线上监控、灰度发布、安全监测等，也必将成为微前端领域接下来要重点解决的问题。