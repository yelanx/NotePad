> 本文由 [简悦 SimpRead](http://ksria.com/simpread/) 转码， 原文地址 [juejin.cn](https://juejin.cn/post/6955129410705948702)

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/913ca2bb92624383b9c0054a4988f239~tplv-k3u1fbpfcp-zoom-1.image)

是从 Vue 2 开始学基础还是直接学 Vue 3 ？**尤雨溪给出的答案是：**“直接学 Vue 3 就行了，基础概念是一模一样的。”

以上内容源引自最新一期的《程序员》期刊，原文链接为[《直接学 Vue 3 吧 —— 对话 Vue.js 作者尤雨溪》](https://mp.weixin.qq.com/s/K9Fb5lMxD2WbxQrk6y6sZA)。

前言
--

`Vue 3.0` 出来之后，我一直在不断的尝试学习和接受新的概念。没办法，作为一个前端开发，并且也不是毕业于名校或就职于大厂，不断地学习，培养学习能力，才是我们这些普通前端开发的核心竞争力。

当然，有些同学抬杠，我专精一门技术，也能开发出自己的核心竞争力。好！！！有志气。但是多数同学，很难有这种意志力。如 `CSS` 大佬[张鑫旭](https://www.zhangxinxu.com/)、 `Canva` 大佬[老姚](https://juejin.cn/user/78820536232855)、可视化大佬[月影大大](https://juejin.cn/user/712139263189303)、面试题大佬[敖丙](https://juejin.cn/user/4406498333825357)等等等等。这些大佬在一件事情上花费的精力，是需要极高的意志力和执行力才能做到的。我反正做不到（逃）。

学无止境！

一定要动手敲代码。仅仅学习而不实践，这种做法也不可取。

本文主要是介绍一些我学习 `Vue 3.0` 期间，看过的一些比较有用的资源，和大家分享一下，不喜勿喷，喷了我也学着 @尼克陈 顺着网线找到你家。 ![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/86b6d7fe1b4f409b9155576e12adcd21~tplv-k3u1fbpfcp-zoom-1.image)

我与 Vue 3.0
----------

其实一直都有在关注 Vue 3.0 相关的进度和新闻，不过真正学习是在它正式 release 后，2020 年 9 月我也发布了一篇文章[《Vue 3.0 来了，我们该做些什么？》](https://juejin.cn/post/6874604408030789640)阐述了自己的看法，也制定了自己的学习计划。

其实，学习任何一门新技术的步骤都一样：

看文档 → 学习新语法 → 做小 demo → 做几个实战项目 → 看源码 → 整理心得并分享。

学习 Vue 3.0 亦是如此，虽然我这个人比较爱开玩笑，也爱写段子，标题取的也吊儿郎当，但是学习和行动起来我可不比别人差。

学习过程中看文档、做 demo，然后也一直在学习和分享 Vue3 的知识点，比如发布一些 Vue3 的教程：

*   [《Vue 3.0 来了，我们该做些什么？》](https://juejin.cn/post/6874604408030789640)
*   [《Vue3 实战系列：结合 Ant-Design-of-Vue 实践 Composition API》](https://juejin.cn/post/6882393804310052871)
*   [《Vue3 来了，Vue3 开源商城项目重构计划正式启动！》](https://juejin.cn/post/6884991023811215374)
*   [《Vue3 实战系列：Vue3.0 + Vant3.0 搭建种子项目》](https://juejin.cn/post/6887590229692121096)
*   [《🎉🎉一个基于 Vue 3 + Vant 3 的开源商城项目🎉🎉》](https://juejin.cn/post/6892783570016796679)
*   [《Vue3 教程：用 Vue3 开发小程序，这里有一份实践代码！》](https://juejin.cn/post/6895360073460416525)
*   [《Vue3 教程：Vue 3.x 快在哪里？》](https://juejin.cn/post/6903171037211557895)
*   [《Vue3 教程：开发一个 Vue 3 + element-plus 的后台管理系统》](https://juejin.cn/post/6942251234191654949)
*   [《🎉🎉Vue 3 + Element Plus + Vite 2 的后台管理系统开源啦🎉🎉》](https://juejin.cn/post/6945072070132760590)
*   [程序员的副业：写了一个专栏《Vue 3 企业级项目实战》](https://juejin.cn/post/6947703226128924702)

也做了几个 Vue 3.0 实战的项目练手，之后发布到也开源了 GitHub 中，访问地址如下：

> in GitHub : [github.com/newbee-ltd](https://github.com/newbee-ltd)
> 
> in Gitee : [gitee.com/newbee-ltd](https://gitee.com/newbee-ltd)

一个是 Vue3 版本的商城项目：

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/cc3382607a2a46a2944d59d4ad7cf3be~tplv-k3u1fbpfcp-zoom-1.image)

一个是 Vue3 版本的后台管理项目：

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/1482ea27549d4462bfc97ca14765fd2a~tplv-k3u1fbpfcp-zoom-1.image)

源码全部开放，后台 API 也有，都是很实用的项目。目前的反响还不错，得到了很多的正向反馈，这些免费的开源项目让大家有了一个不错的 Vue3 练手项目，顺利的完成了课程作业或者在简历里多了一份项目经验，因此也收到了很多感谢的话。

接下来就是学习过程中，我觉得非常有用的资源了，大家在学习 Vue 3 时可以参考和使用。

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/bcd33c72e2c644e7b860fd885b316e21~tplv-k3u1fbpfcp-zoom-1.image)

Vue 3.0 相关技术栈
-------------

<table><thead><tr><th>相关库名称</th><th>在线地址 🔗</th></tr></thead><tbody><tr><td>Vue 3.0 官方文档 (英文)</td><td><a href="https://v3.vuejs.org/" target="_blank" rel="nofollow noopener noreferrer">在线地址</a></td></tr><tr><td>Vue 3.0 中文文档</td><td><a href="https://v3.cn.vuejs.org/" target="_blank" rel="nofollow noopener noreferrer">在线地址</a> <a href="https://vue3js.cn/docs/zh/" target="_blank" rel="nofollow noopener noreferrer">国内加速版</a></td></tr><tr><td>Composition-API 手册</td><td><a href="https://vue3js.cn/vue-composition-api/" target="_blank" rel="nofollow noopener noreferrer">在线地址</a></td></tr><tr><td>Vue 3.0 源码学习</td><td><a href="https://vue3js.cn/start/" target="_blank" rel="nofollow noopener noreferrer">在线地址</a></td></tr><tr><td>Vue-Router 官方文档</td><td><a href="https://next.router.vuejs.org/" target="_blank" rel="nofollow noopener noreferrer">在线地址</a></td></tr><tr><td>Vuex 4.0</td><td><a href="https://github.com/vuejs/vuex/tree/4.0" target="_blank" rel="nofollow noopener noreferrer">Github</a></td></tr><tr><td>vue-devtools</td><td><a href="https://github.com/vuejs/vue-devtools/releases" target="_blank" rel="nofollow noopener noreferrer">Github</a>(Vue3.0 需要使用最新版本)</td></tr><tr><td>Vite 源码学习</td><td><a href="https://vite-design.surge.sh/guide/" target="_blank" rel="nofollow noopener noreferrer">线上地址</a></td></tr><tr><td>Vite 2.0 中文文档</td><td><a href="https://cn.vitejs.dev/" target="_blank" rel="nofollow noopener noreferrer">线上地址</a></td></tr><tr><td>Vue3 新动态</td><td><a href="https://github.com/vue3/vue3-News" target="_blank" rel="nofollow noopener noreferrer">线上地址</a></td></tr></tbody></table>

[Vue3 新动态](https://github.com/vue3/vue3-News) 这个仓库我经常看，里面有最新的 Vue 3 文章、仓库等等，都是中文的，作者应该是咱们的大兄弟，大家也可以关注一下。

更新 Vue 3.0 的开源 UI 组件库
---------------------

`Vue 2.0` 时期，产生了不少好的开源组件库，这些组件库伴随着我们的成长，我们看看哪些组件库更新了 `Vue 3.0` 版本。

#### Element-plus

**简介**：大家想必也不陌生，它的 `Vue 2.0` 版本是 `Element-UI`，后经[坤哥](https://juejin.cn/user/3315782798806430)和他的小伙伴开发出了 `Vue 3.0` 版本的  `Element-plus`，确实很优秀，目前点赞数快破万了，持续关注。

**仓库地址** 🏠 ：[github.com/element-plu…](https://github.com/element-plus/element-plus) ⭐ ： **9.8k**

**文档地址** 📖 ：[element-plus.gitee.io/#/zh-CN](https://element-plus.gitee.io/#/zh-CN)

**开源项目** 🔗 ：

*   [Vue 3.0 + Vite 2.0 + Vue-Router 4.0 + Element-Plus + Echarts 5.0 + Axios 开发的后台管理系统](https://github.com/newbee-ltd/vue3-admin) ⭐ ： **419**
*   [Vue3.0+TypeScript+NodeJS+MySql 编写的一套后台管理系统](https://github.com/xiaoxian521/CURD-TS) ⭐ ： **262**

目前 `Element-plus` 的开源项目还不多，之前 `Element-UI` 相关开源项目，大大小小都在做 `Element-plus` 的适配。在此也感谢[坤哥](https://juejin.cn/user/3315782798806430)和他的小伙伴们，持续 `Element` 系列的维护，这对 `Vue` 生态是非常强大的贡献。

#### Ant Design of Vue

**简介**：它是最早一批做 `Vue 3.0` 适配的组件库， `Antd` 官方推荐的组件库。

**仓库地址** 🏠 ：[github.com/vueComponen…](https://github.com/vueComponent/ant-design-vue/) ⭐ ： **14.8k**

**文档地址** 📖 ：[antdv.com/docs/vue/in…](https://antdv.com/docs/vue/introduce-cn/)

**开源项目** 🔗 ：

*   [AntdV 后台管理系统](https://github.com/iczer/vue-antd-admin) ⭐ ： **2.8k**
*   [vue3.x + ant-design-vue（beta 版本，免费商用，支持 PC、平板、手机）](https://github.com/chuzhixin/vue-admin-better) ⭐ ： **8.2k**
*   [基于 Vue3.0 + Vite + Ant Design Vue](https://github.com/lirongtong/miitvip-vue-admin-manager) ⭐ ： **74**

他们的更新维护还是很积极的，最近一次更新实在 2021 年 2 月 27 号，可见这个组件库还是值得信赖的，有问题可以去 issue 提。

#### Vant

**简介**：国内移动端首屈一指的组件库，用过的都说好，个人已经在两个项目中使用过该组件库，也算是比较早支持 `Vue 3.0` 的框架，该有的都有。

**仓库地址** 🏠 ：[github.com/youzan/vant](https://github.com/youzan/vant) ⭐ ： **16.9k**

**文档地址** 📖 ：[vant-contrib.gitee.io/vant/v3/#/z…](https://vant-contrib.gitee.io/vant/v3/#/zh-CN)

**开源项目** 🔗 ：

*   [newbee-mall Vue3 版本](https://github.com/newbee-ltd/newbee-mall-vue3-app)⭐ ： **1.7k**
*   [高仿微信记账本](https://github.com/Nick930826/daily-cost) ⭐ ： **48**
*   [仿京东淘宝电商](https://github.com/GitHubGanKai/vue3-jd-h5)  ⭐ ： **319**

#### NutUI 3

**简介**：京东团队开发的移动端组件库，近期才升级到 `Vue 3.0` 版本，[文章在此](https://juejin.cn/post/6935231099102560293)。虽然我没有使用过这个组件库，但是从他们的更新速度来看，比其他很多组件库要快，说明对待最近技术，还是有态度的。

**仓库地址** 🏠 ：[github.com/jdf2e/nutui](https://github.com/jdf2e/nutui) ⭐ ： **3.1k**

**文档地址** 📖 ：[nutui.jd.com](https://nutui.jd.com/#/index) （看看这简短的域名，透露出壕的气息）

**开源项目** 🔗 ：基本上还没有见到有公开的开源项目，如果有还望大家积极评论。

Vue 3.0 实战视频学习
--------------

<table><thead><tr><th>相关库名称</th><th>在线地址 🔗</th></tr></thead><tbody><tr><td>Vue 3.0 实战星座物语 H5 项目</td><td><a href="https://www.bilibili.com/video/BV1Q64y1F7mm?from=search&amp;seid=15048255084253288459" target="_blank" rel="nofollow noopener noreferrer">在线地址</a></td></tr><tr><td>Vue 3.0 UI 组件库开发</td><td><a href="https://www.bilibili.com/video/BV1ny4y1i7Sh?from=search&amp;seid=15048255084253288459" target="_blank" rel="nofollow noopener noreferrer">在线地址</a></td></tr><tr><td>Vue 3.0 + Vite 手册阅读</td><td><a href="https://www.bilibili.com/video/BV1Q54y1k7At?from=search&amp;seid=15048255084253288459" target="_blank" rel="nofollow noopener noreferrer">在线地址</a></td></tr><tr><td>Vue 3.0 入门之项目搭建（杨村长）</td><td><a href="https://www.bilibili.com/video/BV1vX4y1K7bQ?from=search&amp;seid=17184556019333060655" target="_blank" rel="nofollow noopener noreferrer">在线地址</a></td></tr><tr><td>Vue 3.0 入门（技术胖）</td><td><a href="https://www.bilibili.com/video/BV1L5411j7vj?from=search&amp;seid=17184556019333060655" target="_blank" rel="nofollow noopener noreferrer">在线地址</a></td></tr><tr><td>Vite 2.0 插件开发指南</td><td><a href="https://www.bilibili.com/video/BV1jb4y1R7UV?from=search&amp;seid=384387825939775015" target="_blank" rel="nofollow noopener noreferrer">在线地址</a></td></tr><tr><td>Vue 3.0 + Vite 2.0 快速搭建 Electron 应用</td><td><a href="https://www.bilibili.com/video/BV1XV411e7Hq?from=search&amp;seid=384387825939775015" target="_blank" rel="nofollow noopener noreferrer">在线地址</a></td></tr></tbody></table>

> 上述视频，本人都学习过，质量可控，希望对大家入门 Vue 3.0 有帮助。

总结
--

学习是一辈子的事，不光指学习计算机技术，生活的方方面面都需要保持一个学习的心，祝大家学习愉快。

> 除注明转载 / 出处外，皆为作者原创，欢迎转载，但未经作者同意必须保留此段声明，且在文章页面明显位置给出原文链接，否则保留追究法律责任的权利。