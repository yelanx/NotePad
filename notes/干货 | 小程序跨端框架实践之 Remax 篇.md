> 本文由 [简悦 SimpRead](http://ksria.com/simpread/) 转码， 原文地址 [mp.weixin.qq.com](https://mp.weixin.qq.com/s/K4r31sDS2cUUoZ8gMy5KKw)

**作者简介** 

汽车票前端研发团队，致力于提供更便捷更智慧的出行方式，关注前端技术方向的探索和实践。

**一、项目背景**
----------

随着小程序在用户规模和商业化上取得的极大成功，各大平台都推出了自己的小程序。然而这些平台的小程序开发在语法上又不尽相同，不同平台小程序代码的维护需要投入很大的精力，在逻辑性上也很难达到统一的效果。虽然也有各种转换工具可以基于某一个平台转换出其他平台的代码，但转换的效果也是差强人意，往往还需要人工去修改。使用小程序跨端开发框架来实现一次开发、到处运行以提升效率，已经成为开发者强烈而迫切的需求。

目前，小程序跨端开发框架主要可以按照技术栈和实现原理两个维度进行分类。从技术栈来说，主流的跨端框架基本遵循 React、Vue 这两个前端开发最常使用的框架。由于所在团队主要使用的是 React，所以本文主要介绍采用 React 语法的框架。从实现原理上，开源社区的跨端框架可分为**编译时**（compile time）和**运行时**（runtime）。

主流框架及其特点介绍如下表 1-1 所示：

  **表 1-1 React 语法小程序跨端框架举例**

<table cellspacing="0" cellpadding="0" align="center"><tbody><tr><td valign="middle" colspan="1" rowspan="1" align="center" width="74.33333333333333"><strong>框架<br></strong></td><td valign="middle" colspan="1" rowspan="1" align="center" width="76.33333333333333"><strong>厂家<br></strong></td><td valign="middle" colspan="1" rowspan="1" align="center" width="328.3333333333333"><strong>特征<br></strong></td></tr><tr><td valign="middle" width="74.33333333333333" align="center"><p>Kbone</p></td><td valign="middle" width="76.33333333333333" align="center"><p>腾讯</p></td><td valign="top" width="333"><p>不限技术栈，微信小程序和 Web 端同构的<strong>运行时</strong>解决方案，模拟了一套 dom 和 bom 接口，用以兼容现有的前端体系，只能用于 Web 兼容微信小程序，无法满足其他平台小程序的开发</p></td></tr><tr><td valign="middle" width="73.33333333333333" align="center"><p>Taro1/2</p></td><td valign="middle" width="76.33333333333333" align="center"><p>京东</p></td><td valign="top" width="333"><p>类 React，<strong>静态编译型框架</strong>仅在开发时遵循 React 语法，编译后运行时与 React 无关</p></td></tr><tr><td valign="middle" width="73.33333333333333" align="center"><p>Nanachi</p></td><td valign="middle" width="76.33333333333333" align="center"><p>去哪儿</p></td><td valign="top" width="333"><p>React，<strong>静态编译型框架</strong></p></td></tr><tr><td valign="middle" width="73.33333333333333" align="center"><p>Rax</p></td><td valign="middle" width="76.33333333333333" align="center"><p>阿里巴巴</p></td><td valign="top" width="333"><p>以<strong>运行</strong><strong>时</strong>方案为基础，支持局部场景使用<strong>编译时</strong>方案。运行时的支持基于 Kbone，使用的是类 React 语法的 Rax 框架</p></td></tr><tr><td valign="middle" width="73.33333333333333" align="center"><p>Remax</p></td><td valign="middle" width="76.33333333333333" align="center"><p>蚂蚁金服</p></td><td valign="top" width="333"><p>使用原生 React 来构建小程序，<strong>运行时</strong>框架，从 Remax2.0 开始支持 Web 应用的构建</p></td></tr><tr><td valign="middle" width="73.33333333333333" align="center"><p>Taro3</p></td><td valign="middle" width="76.33333333333333" align="center"><p>京东</p></td><td valign="top" width="333"><p>不限技术栈，使用一套 runtime 层来兼容各种 DSL，诞生于 Remax 之后</p></td></tr></tbody></table>

compile time **编译时**的跨端框架，主要的工作量在编译阶段。框架会把用户写的业务代码解析成 AST 树，然后通过语法分析将用户写的原始代码转换成符合小程序规则的代码。运行时模式的跨端框架通过适配层，实现自定义渲染器，是真正的在小程序的逻辑层中运行起 React 或 Vue 框架的方式，这种方式比静态编译有天然的优势。

编译时主要存在以下问题：灵活的 JSX 语法既可以写出非常复杂灵活的组件，同时也增加了编译阶段框架去分析和优化的难度。这就导致适配工作量巨大，维护成本较高，即使这样，也无法适配所有的写法。例如京东的 Taro 1/2 用穷举的方式对 JSX 可能的写法进行了一一适配，但依然需要开发者遵循大量的语法约束，避免很多动态写法。否则代码就不能正常编译运行，开发效率难以保证。此外，由于 DOM 和 BOM API 的缺失，Web 上累积的各种前端生态，基本无法在编译时小程序中复用。京东的 Taro 1/2 , 去哪儿的 Nanachi 都属于静态编译型的 React 或类 React 跨端框架。

与之相比，**运行时**方案的优势就在于能直接复用现有的前端生态。拿 Remax 来说，它最大的优势是可以几乎没有限制的使用 React 的语法完成代码编写，正如它的口号一样——使用真正 React 来构建跨平台小程序。此外，从 Remax2.0 开始，remax/one 支持 Web 应用的构建。

我们团队做选型的时候 Taro3 还是待发布状态，所以没有做太多的考虑，下面着重去比较一下 Rax 和 Remax。Rax 和 Remax 都是出自阿里系但两个框架从设计思路上完全不同。

Remax 从诞生之初就是为了支持小程序跨端框架 Rax 为了能尽量压缩 React 体积对 React 进行了重写，又引入了 Driver 机制来适配多端，这就意味着 Rax 有额外的学习成本，并且 Rax 不能随着 React 的迭代而更新。虽然 Rax 看似比较完善，提供了一套开箱即用的跨端 API 和完整的跨端 UI 控件支持，但它过多的依赖阿里的构建体系，似乎不太适合做为开源框架的选择。

综合以上几点考虑最终我们选择了 Remax。

**二、效果展示**
----------

图 2-1 到 2-3 分别为首页，列表页和详情页在 Web 和微信小程序上的运行效果，实现了一套代码多端运行，同时能做到同步更新。

<table><tbody><tr><td width="260" valign="top"><p><img class="" src="https://mmbiz.qpic.cn/mmbiz_jpg/kEeDgfCVf1esDqmQ8SqpT0KqwJP8ibLLEABHryqL7RkK6sdxqEKJVJKrCUZTp6WgAWlbGQLHCK8VI9eL8giceIHg/640?wx_fmt=jpeg" style="width: auto;"></p></td><td width="260" valign="top"><p><img class="" src="https://mmbiz.qpic.cn/mmbiz_jpg/kEeDgfCVf1esDqmQ8SqpT0KqwJP8ibLLEtHupybQ6OQk0qhuQAvKQ8cWXmKMNfCgH0lgoedKJ6I2gJxQwicMlu8A/640?wx_fmt=jpeg" style="width: auto;"></p></td></tr></tbody></table>

图 2-1 携程船票首页 web 和微信小程序运行结果  

<table><tbody><tr><td width="260" valign="top"><p><img class="" src="https://mmbiz.qpic.cn/mmbiz_jpg/kEeDgfCVf1esDqmQ8SqpT0KqwJP8ibLLEJ02Jge5nPOdIFdib9ZZia1szoicc1FHt1Rxx5TnEvicTy7Q80Viap3CFRVg/640?wx_fmt=jpeg" style="width: auto;"></p></td><td width="260" valign="top"><p><img class="" src="https://mmbiz.qpic.cn/mmbiz_jpg/kEeDgfCVf1esDqmQ8SqpT0KqwJP8ibLLEFsCEsm0lbhWNvvhwpVwykvWbju9qyTb2NfjL4X0rTMI7pXuWBFO2zQ/640?wx_fmt=jpeg" style="width: auto;"></p></td></tr></tbody></table>

图 2-2 携程船票列表页 web 和微信小程序运行结果  

<table><tbody><tr><td width="260" valign="top"><p><img class="" src="https://mmbiz.qpic.cn/mmbiz_jpg/kEeDgfCVf1esDqmQ8SqpT0KqwJP8ibLLE7WicSn17bLMoLZOhfrXu4GBw7b07BW2wBpTtic7V5sfhkYb3tyw1JiafA/640?wx_fmt=jpeg" style="width: auto;"></p></td><td width="260" valign="top"><p><img class="" src="https://mmbiz.qpic.cn/mmbiz_jpg/kEeDgfCVf1esDqmQ8SqpT0KqwJP8ibLLEXMxZFlh2Bp5mRictcPrKD8JNS0x1UT8BZRTSzhrhX6mp1ejzRDE6K2g/640?wx_fmt=jpeg" style="width: auto;"></p></td></tr></tbody></table>

图 2-3 携程船票详情页 web 和微信小程序运行结果  

下面介绍一下 Remax 的基本用法。

**三、基本用法**
----------

### **3.1 创建 / 安装依赖 / 运行**

使用 create-remax-app 创建的小程序

```
npxcreate-remax-app my-app
? name my-app //填写你的appName
? author test<test@test.c>
? descriptionRemax Project  //填写项目描述
? platform 
❯ 跨平台小程序
  微信小程序
  阿里（支付宝）小程序
  头条小程序
  百度小程序
```

因为我们介绍的是跨端小程序，这里选择跨平台小程序。

运行项目

```
cd my-app&& npm install
npm run dev web//web端预览
npm run devwechat //微信小程序端
```

小程序端运行后会在项目目录里生成 dist/wechat、dist/ali 各个小程序的产物，用小程序的 IDE 导入对应的目录即可预览。  

项目的目录结构大致如下：

![](https://mmbiz.qpic.cn/mmbiz_png/kEeDgfCVf1f8KElOWyicoAlJPMG8ZoRhjWeOW8pV1NGAwB8yEbmkoIlvKeickk9Se035fsQPyvicboEia9rIkGibIeA/640?wx_fmt=png)

其中 public 目录在编译时会被复制到 dist 目录下，里面的原生页面 pages 目录也会和 Remax 的 pages 目录合并，这一部分后面会详细介绍。  

### **3.2 Remax** **的跨平台机制**

以下详细介绍 Remax 的跨平台机制

![](https://mmbiz.qpic.cn/mmbiz_png/kEeDgfCVf1f8KElOWyicoAlJPMG8ZoRhjfSPsYae3W6rRoraSDTRww2miaAI68eILQdLv8suT8I0vMdYOl48gBhA/640?wx_fmt=png)

以上就是一个页面的基本文件目录结构，但是我们翻一遍 Remax 的文档就会发现它提供的跨平台的接口是少之又少，只有 9 个组件以及 5 个路由相关的 API。所以 Remax 又提供了一种用文件名后缀来区分不同平台代码的方法：

![](https://mmbiz.qpic.cn/mmbiz_png/kEeDgfCVf1f8KElOWyicoAlJPMG8ZoRhjl2XVdmZ9WJ2gXLy5zXWE1T0fZlia5ia03IYztrGwtHziclyylrp7BNTUg/640?wx_fmt=png)

如上面的目录所示，在页面的目录里增加相应后缀的文件在 build 相应平台的时候就会优先使用对应后缀的文件。

Remax 还提供了环境变量区分平台的机制，在代码中直接通过 process.env.REMAX_PLATFORM 区分平台。例如：

```
if (process.env.REMAX_PLATFORM==='wechat') {}
```

以上代码在编译后只会保留对应平台的部分，所以无需担心兼容多平台带来额外的代码 size 增加。

除了上面说到的 9 个跨平台的组件和 5 个跨平台的 API 外，Remax 还可以直接使用各个平台的组件及 API，无需使用 useComponents 声明。

```
import React from'react';
import { View } from'remax/one';
import NativeCard from'./native-card';//native-card 是一个原生自定义组件
// 组件
exportdefault () => (
  <View>
    <NativeCard />
  </View>
);
// API
if (process.env.REMAX_PLATFORM==='ali') {
    systemInfo = my.getSysyemInfoSync();
} elseif (process.env.REMAX_PLATFORM==='wechat') {
    systemInfo = my.getSysyemInfoSync();
}
```

根据以上机制我们就可以自定义出任何我们需要的跨平台 API 及组件。

按照 Remax 的说法之所以不提供更多的跨平台组件和 API 是因为他们在设计之初没有标准去抹平各个平台的差异。这当然给使用框架的开发者带来了一些麻烦，许多的组件和 API **不能开箱即用**，需要额外的去做一层封装。但这也是 Remax 这个框架的优势，只保留了核心的组件及 API 使得它本身不占太多的 size，也使得要兼容一个新的平台变得非常容易。

**四、实践干货**
----------

### **4.1 混合原生**

之前我们说到了 public 目录里的原生代码会拷贝到 dist 目录并且 pages 目录会合并，我们可以利用这一机制最大程度的复用现有小程序的代码，这也是我们团队选择 Remax 这个框架的原因之一。

让我们来体验一下，先创建一个原生小程序，以微信小程序为例：

小程序的文件目录大致如下

![](https://mmbiz.qpic.cn/mmbiz_png/kEeDgfCVf1f8KElOWyicoAlJPMG8ZoRhjnBErArxjUjSWFq9tqQYKhcUML99tNO9yGBTHNZtCCswCUC46R3UvAA/640?wx_fmt=png)

‍  

现在把整个小程序的代码放入 public 目录

![](https://mmbiz.qpic.cn/mmbiz_png/kEeDgfCVf1f8KElOWyicoAlJPMG8ZoRhjnb6ZwFnwAkownaE0JtNicGaBicgom0RwOFhPFFgTvZJE5GdicYLxAIP7w/640?wx_fmt=png)

此时 npm run dev wechat 就会把 pages 以及 utils 拷贝到 dist 目录下并且 pages 目录合并了。

![](https://mmbiz.qpic.cn/mmbiz_png/kEeDgfCVf1f8KElOWyicoAlJPMG8ZoRhjk78aricXFglGSS4TIMDQdEumQbNWiat1aYDgRkELrFhBXs5okTgRRBZA/640?wx_fmt=png)

虽然合并了，但是我们观察一下发现原生小程序的 app.json 的内容不见了，难道要在 Remax 里面把 app.json 都写一遍么？带着疑问我们仔细翻看 Remax 的文档，发现 remax.config.js 里是可以配置动态生成 app.json 的配置的：

```
onAppConfig() {
    ...
      // 获取原始小程序配置originConfig和 remax app.config.js配置tmp
    // 做合并处理
    const appJSON =JSON.parse(JSON.stringify(originConfig));
    tmp.pages.forEach(function (item) {
        if (appJSON.pages.indexOf(item) ==-1) {
            appJSON.pages.push(item);
        }
    });
    tmp.subPackages.forEach(function (item) {
        let needAdd =true;
        for (let i =0, a = appJSON.subPackages; i < a.length; i++) {
            if (a[i].root=== item.root) {
                needAdd =false; a[i] = item;break;
            }
        }
        if (needAdd) {
            appJSON.subPackages.push(item);
        }
    });
    ...
    return appJSON;
}
```

经过以上处理，原小程序的 app.json 内容就和 remax.config.js 内容合并了，以上代码只处理了 pages 和 subPackages，如果觉得还有什么需要合并的也可以在这里处理。此时，build 生成的产物 app.json，就保留了原小程序的内容并且合并了 Remax 小程序的内容。

产物里的 app.js 也没有原生小程序里的代码，那原有的逻辑怎么办呢。重新在 Remax 写一遍吗？大可不必。

我们可以自定义一个运行时插件：

```
function createBothFun(remaxConfig, originConfig, key) {
    const remaxFun = remaxConfig[key];
    const originFun = originConfig[key];
    return function () {
        // 这里的this就是微信的app 里的this
        remaxFun.apply(this,arguments);
        originFun.apply(this,arguments);
    };
}
const lifeCycles=['onLaunch','onShow','onHide','onPageNotFound','onError']
function tryMergeConfig(remaxConfig, originConfig) {
    for (const key in originConfig) {
        if (key ==='constructor') {
            console.log('xxx');
        } elseif (lifeCycles.indexOf(key) >=0) {
            remaxConfig[key] =createBothFun(remaxConfig, originConfig, key);
        } else {
            remaxConfig[key] = originConfig[key];
        }
    }
}
const mergeConfig = (remaxConfig, originConfig) => {
    tryMergeConfig(remaxConfig, originConfig);
    return remaxConfig;
};
export default {
    onAppConfig({ config }) {
        let __app = App;
        let originConfig;
        App =function (origin) {
            originConfig = origin;
        };
        __non_webpack_require__('./app-origin.js');
        App = __app;
        //merge config
        config =mergeConfig(config, originConfig);
        const onLaunch = config.onLaunch;
        config.onLaunch= (...args) => {
            if (onLaunch) {
                onLaunch.apply(config, args);
            }
        };
        return config;
    },
};
```

把原来的 app.js 重命名为 app-origin.js，然后在 onAppConfig 函数中使用__non_webpack_require__('./app-origin.js'); 请注意，这里的相对路径是产物里的相对路径，经过以上一番操作后，我们的原小程序就可以真正和 Remax 混合运行了。

但是这样一来，我们的 Remax 似乎变得不能跨端了，因为它只能编译成你 public 目录里放置的原生小程序类型。

难道混合和跨端只能是鱼和熊掌不可兼得？后面我们将介绍使用工程化的方法来实现**鱼和熊掌兼得**。

### **4.2 Modal API** **化**

你可能注意到了 Remax 的文档里面列举了 10 个控件，但我说它只有 9 个控件，官方的文档上也说只有 9 个控件，为什么呢？因为 Modal 严格的说来不能算是一个控件。

Modal 实际上是调用 createPortal 这个 API 来创建一个节点覆盖在其它内容之上，在 web 端是使用的 ReactDOM 的 createPortal，在小程序端是使用的 @remax/runtime 这个包里提供的同名方法。实际上 portal 在两端挂载的位置也不太一样，在 Web 端是直接的 body 上创建了一个新的 div，而在小程序上则是挂在页面实例一个叫 modalContainer 的节点。在实际使用中，使用 Modal 组件去显示弹窗非常不方便，所以还是得把它变成 API 调用的方式。

以小程序端为例：

```
import...
import { createPortal } from'@remax/runtime';
import {ReactReconcilerInst } from'@remax/runtime/esm/render';

let createPortal__ = createPortal;
let ReactReconcilerInst__ = ReactReconcilerInst;
const styles = {
    modalContainer: {
        ...
    },
};
export default function withModal(TargetComponent) {
    const WrappedModalComponent = (props) => {
        const { mask,...other } = props;
        const component =useRef();
        const container =getCurrentPage().modalContainer;
        return createPortal__(
                  <View style={{ ...styles.modalContainer,pointerEvents: mask ?'auto':'none' }}>
                      <TargetComponent {...other} mask={mask} show={show} close={close} ref={component} />
                  </View>,
                  container
              )
    };
    WrappedModalComponent.hide= (conext) => {
        const container =getCurrentPage().modalContainer;
        if (container._rootContainer) {
            ReactReconcilerInst__.updateContainer(null, container._rootContainer,null,function () {
            });
        }
        container.applyUpdate();
    };
    WrappedModalComponent.show= (props) => {
        const container =getCurrentPage().modalContainer;
        if (!container._rootContainer) {
            container._rootContainer=ReactReconcilerInst__.createContainer(container,false,false);
        }
        const element = React.createElement(WrappedModalComponent, props);
        ReactReconcilerInst__.updateContainer(element, container._rootContainer,null,function () {
        });
        context.modalContainer.applyUpdate();
    };
    return WrappedModalComponent;
}
export { withModal };
```

使用示例：

```
//在需要弹窗的组件上使用withModal装饰器
@withModal
export default MyComponent(props) {
  ...
  return<View>{...}</View>
}
//在不支持装饰器的模式下也可以直接调用
function MyComponent(props) {
    ...
  return<View>{...}</View>
} 
const ModaledComponent =withModal(MyComponent)
//在需要使用弹窗的地方使用
ModaledComponent.show(props);//展示弹窗
ModaledComponent.hide();
```

### **4.3** **工程化**

考虑到我们的小程序是多部门多团队共同合作的项目，不可能让整个公司同时都使用 Remax 重写原来的业务，那样会有极大不可控风险。所以 Remax 只能是在部分业务试用，并且能渐进的切换原有的业务，这就要求我们必须有工程化的方案。

我们期望的小程序产物的结构如下:

![](https://mmbiz.qpic.cn/mmbiz_png/kEeDgfCVf1f8KElOWyicoAlJPMG8ZoRhj0qTLJhewibTmKwT2jicEEMkuAATrbTXOazYP4LX9bnKGB81EVqqNIz0A/640?wx_fmt=png)

而 Web 端的产物结构如下：

![](https://mmbiz.qpic.cn/mmbiz_png/kEeDgfCVf1f8KElOWyicoAlJPMG8ZoRhj3nxiclMnwv3L3gNn4ldgvzITc8b4uhicyGvuialMVibDL2mz5Ecc15ZHNA/640?wx_fmt=png)

这表示小程序端是依赖原有小程序的，Web 端是可以单个业务单独发布的，于是我们在编译过程中给小程序和 Web 生成两套不同的壳工程。

编译小程序的过程中拉取壳工程，壳工程的目录结构大致如下:

![](https://mmbiz.qpic.cn/mmbiz_png/kEeDgfCVf1f8KElOWyicoAlJPMG8ZoRhjXEVMF5e4kiaLqkkgLvib8hD1Apj2zdJaTPHKgPBrPoyft0FicmkGVG4rg/640?wx_fmt=png)

其中 remaxA 和 remaxB 的页面代码是在拉取壳工程的时候动态生成的，我们在壳工程里放入一个配置文件 bundle.js，用来描述该壳工程有哪些 Remax 业务代码：

```
module.exports= {
  remaxA: {
    git:"git@remaxA.git"
  },
  remaxB: {
    gitL "git@remaxB.git"   
    }
}
```

在拉取壳工程的同时 clonebundle.js 所配置的仓库到临时目录 packages，此时 packages 目录如下：

![](https://mmbiz.qpic.cn/mmbiz_png/kEeDgfCVf1f8KElOWyicoAlJPMG8ZoRhj4f0D6HnTgT337ewS46JdFcQbqIavXlVcWCLXr4Ubm3icTdicxyGsz8Qg/640?wx_fmt=png)

然后根据 Remax 业务代码里的 app.config.js，重新在壳工程生成新的页面和页面配置，其核心逻辑如下:

```
const template = (projectName, path) => {
    return`import ${projectName} from'~packages/${projectName}/src/pages${path ?`/${path}`:''}';
${projectName}.prototype.onShareAppMessage;
${projectName}.prototype.onPageScroll;
${projectName}.prototype.onShareTimeline;
exportdefault ${projectName};
`;
}

const pageHandler = (projectName) => {
    const projectPath =`${rootDir}/packages/${projectName}`;
    shell.cd(projectPath);
    let conf =require(`${projectPath}/src/app.config.js`);
    let platConf = conf[platform] || conf;
    const projectAllPages = [];
    ...
    // 遍历 pages 和subPackages配置并替换路径
    pagePath.replace('pages/',`pages/${projectName}/`);
    ...
    subPackage.root= subPackage.root.replace('pages/',`pages/${projectName}/`);
    ...
    // 将pages subPackages里所有的页面路径合并到一起
    let allPages = [...platConf.pages]
    allPages.push(path.join(subPackage.root, page));
    // 遍历页面配置生成新的页面
    allPages.forEach((mapPath) => {
        const pagePath = path.resolve(rootDir,'src','pages', projectName,`${mapPath}.js`);
        fse.ensureFileSync(pagePath);
        const data =template(projectName, mapPath);
        fs.writeFileSync(pagePath, data);
    });
};
const complier = () => {
    ...
    //获取子项目git地址，下载至packages目录下
    const packagesPath = path.resolve(rootDir,'packages');
    const subDirs = fs.readdirSync(packagesPath);
    // 遍历packages根据packages里的app.config.js重新生成合并路径后的页面
    subDirs.forEach((name) => {
        let file = fs.statSync(`${packagesPath}/${name}`);
        if (file.isDirectory()) {
            pageHandler(name);
        }
    });
    ...
};
module.exports= complier
```

生成的页面代码如下:

```
import remaxA from'~packages/remaxA/src/pages/index/index';
remaxA.prototype.onShareAppMessage;
remaxA.prototype.onPageScroll;
remaxA.prototype.onShareTimeline;
exportdefault remaxA;
```

这个可以修改上面代码中 template 按需求修改, 这段代码中之所以有类似 remaxA.prototype.onShareAppMessage; 这样的无用代码是因为 Remax 在编译过程中会收集页面代码里的生命周期函数的关键字，非必需的生命周期在页面代码没有出现的时候，编译产物里也不会有。

生成的页面路径如下:

![](https://mmbiz.qpic.cn/mmbiz_png/kEeDgfCVf1f8KElOWyicoAlJPMG8ZoRhjaGBibl3lhOneicfSwN50QTn9upLJvoUNVMEYqMcMN1ErN7I58ic3ROzqw/640?wx_fmt=png)

同样的，在 Web 端也会有相似的操作。我们 Web 是使用 node 容器发布的，所以壳工程就弄成了 node 工程了。如果使用静态发布那就不需要壳工程了，直接 build 发布产物就可以了。

此外，由于小程序的单包 size 限制的原因，在小程序 webpack 配置方面需要做一些额外的配置，避免多个 Remax 业务不共同依赖的代码也打到主包去，导致主包的单包 size 超出限制，这里给一个例子，仅供参考：

```
configWebpack:function (options) {
      let config = options.config;
      let subpackageGroups = {};
      Object.keys(projects).forEach((key) => {
          let packagePages = projectsPages[key];
          let allPages = packagePages.allPages.map((page) =>`pages/${key}/${page}`);
          let pages = packagePages.pages;
          subpackageGroups[`${key}Common`] = {
              name:`package-${key}-common`,
              test: (module) =>newRegExp(`[\\/]packages[\\/]${key}[\\/]src[\\/]`).test(module.userRequest),
              chunks:'all',
              minChunks:2,
              minSize:0,
              priority:91,
              filename:`pages/${key}/package-${key}-common.js`,
          };
      });
      config.optimization.merge({
          splitChunks: {
              maxAsyncRequests:100,
              maxInitialRequests:100,
              automaticNameDelimiter:'-',
              enforceSizeThreshold:50000,
              cacheGroups: {
                ...,
                ...subpackageGroups
              },
          },
      });
    },
```

**五、经验总结**
----------

*   经过实践，我们发现 Remax 确实是可以做到一处编写到处运行的，不足之处是 Remax 没有提供一整套开箱即用的跨端 API 和控件，使用这个框架可能会有较多的前期基础工作。但这也是它的优势，核心的依赖少了，而且是完全开放的，不会说用它就会带来一个全家桶的依赖。
    

*   由于 Remax 实现原理的限制，在小程序上较复杂的页面性能上会有不足。当然这也是所有跨端框架存在的问题，好在可以通过使用自定原生组件的方式来解决。
    

*   Remax 目前还不支持 DOM、BOM 接口，也不支持直接使用 webhostcomponent 来编写跨端小程序，所以要实现跨端的话还是需要对现有的 React 应用做一定的改造。
    

*   Remax 还不支持 RN 平台，说不支持并不是说不能支持，只是要兼容 RN 的话，可能还得在现有的 React 上做一些限制。比如 RN 对 css 的支持，以及要实现一整套的 RN 的控件来兼容 web 端及小程序端，这又使得 Remax 不那么纯粹了。
    

**六、写在最后**  

本文旨在给大家提供一些新的思路，在选型方面应该从多方面去考量，各个方案可能没有明显的好坏之分，适合的才是最好的。就拿 Taro 来说，得益于有官方团队的支持，Taro3 的发展速度非常之快，各个方面都做的比较完善。而 Remax 社区似乎没有那么活跃，所以发展速度相对来说比较慢。期待有更多的朋友来参与开源框架的贡献。

**团队招聘信息**

我们是携程火车票研发团队，负责火车票业务的开发以及创新。火车票研发在多种交通线路联程联运算法、多种交通工具一站式预定、高并发方向不断地深入探索和创新，持续优化用户体验，提高效率，致力于为全球人民买全球火车票。

在火车票研发团队，你可以和众多技术大牛一起，真实地让亿万用户享受你的产品和代码，提升全球旅行者出行体验和幸福指数。

如果你也热爱技术，并渴望不断成长，火车票研发团队期待与你一起高速前行。目前我们前端、后台、算法、大数据、测试等技术岗位均有职位。

简历投递：tech@trip.com 邮件标题：【姓名】-【携程火车票】-【投递职位】

**【推荐阅读】**

*   [前端智能化探索，骨架屏低代码自动生成方案实践](http://mp.weixin.qq.com/s?__biz=MjM5MDI3MjA5MQ==&mid=2697271775&idx=2&sn=8f127a61201c4fb797d865e6ff0fc281&chksm=8376e6ebb4016ffd7ce18b15bae683444229a2efd93facd9717e6fa64cb1557f60be2047cc1a&scene=21#wechat_redirect)
    ===============================================================================================================================================================================================================================================
    
*   [React Server Components 初探](http://mp.weixin.qq.com/s?__biz=MjM5MDI3MjA5MQ==&mid=2697271712&idx=2&sn=a8840232dc5870cd2b2c1d41394b8842&chksm=8376e694b4016f822ca901fecbdda3a3b5704943a9d2cfc2a6bd64e99a18b2d58c0207446c4f&scene=21#wechat_redirect)
    
*   [Taro 虚拟列表最佳实践](http://mp.weixin.qq.com/s?__biz=MjM5MDI3MjA5MQ==&mid=2697271653&idx=2&sn=bf9650cfd03fb7910248213b657b823b&chksm=8376e651b4016f4792f0237d9cde94fc2f82c522a871a1c810e89edbd81c197449ad998ebf8a&scene=21#wechat_redirect)
    --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
    
*   [携程门票 H5 转小程序实践](http://mp.weixin.qq.com/s?__biz=MjM5MDI3MjA5MQ==&mid=2697271602&idx=2&sn=a91ee08af1736ff936abc5cf58b740aa&chksm=8376e606b4016f10938e4144d97ca2eca5a377f3b50ff93517eeef79ef4d0d05fd2d014891e7&scene=21#wechat_redirect)
    ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
    

![图片](https://mmbiz.qpic.cn/mmbiz_jpg/kEeDgfCVf1esDqmQ8SqpT0KqwJP8ibLLEibyBlCwMVokVaQpw3ibfrc9GG6RdLRLoehtXQDe1GBjSKXjZshxoCDqQ/640?wx_fmt=jpeg)

 **“携程技术” 公众号**

 **分享，交流，成长**