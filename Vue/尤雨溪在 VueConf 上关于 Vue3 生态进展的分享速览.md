> 本文由 [简悦 SimpRead](http://ksria.com/simpread/) 转码， 原文地址 [juejin.cn](https://juejin.cn/post/6964997137025204260)

> 尤雨溪在 VueConf 上关于 Vue3 生态进展的分享速览：主要包括数据、生态进展，开发体验、兼容性相关等话题

> 今天观看了 vueconf 大会，第一时间跟大家分享一下尤大关于 vue3 生态的分享，官方 ppt 还没有拿到，大家先将就看。

### 数据

主要公布了 devtools 和 vue3 的 npm 下载量增长统计，其中 devtools 增长 43%，npm 下载增长 51%。

可见增长势头之猛。

### 生态进展

*   vue-router4 已稳定
    
*   vuex4 已稳定
    
*   组件库：quarsar，element-plus，ant design vue 等，剩下俩忘了，其中只有 antdv 是正式版，其他处于 beta 状态，有小伙伴气愤移动端最强 vant 也是正式版竟然没被提及。
    

### 开发体验

*   构建工具：没有悬念，尤大强力推荐 vite
    
    *   vite：主要介绍特性、插件机制和 ssr 进度
        
        ![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/ac13ce379e444d30b1ec1255cc725ffc~tplv-k3u1fbpfcp-zoom-1.image)
        
        关于脚手架 vue-cli 和 vite 之间关系，暂时并存，未来将会主推 vite。
        
        ![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/b2735df888f4433ebeef1dfa8c3caac0~tplv-k3u1fbpfcp-zoom-1.image)
        
        推了一把 VitePress，主要是开发体验好，有博客需求的朋友可以试试。
        
        ![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/3216c13ed0b2410482cd1b0c78f0774f~tplv-k3u1fbpfcp-zoom-1.image)
        
*   开发体验改进
    
    *   改善 SFC 书写体验：主要是两个 rfc，分别是 [setup script](https://github.com/vuejs/rfcs/pull/227) 和 [CSS style injection](https://github.com/vuejs/rfcs/pull/231)
        
        ![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/65ecbb7c8c7941599b98513c8e94c4e0~tplv-k3u1fbpfcp-zoom-1.image)
        
    *   setup script：主要简化 SFC 中使用 Composition API 体验，最终版把之前争议比较大的 ref sugar 去掉了。
        
        *   尤大演示了 setup script 能够如何帮助我们简化组件编写：
            
            ```
            <template>
            	<p>{{counter}}</p>
              <Comp></Comp>
            </template>
            <script setup>
            	import Comp from './Comp.vue'
              import {ref} from 'vue'
              const counter = ref(0)
            </script>
            复制代码
            ```
            
        *   推荐了一款体验页面：[Vue SFC Playground](http://sfc.vuejs.org/)
            
            ![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/09216971af7e4570bcec58415bfd47b1~tplv-k3u1fbpfcp-zoom-1.image)
            
    *   style 动态变量注入：在 style 标签中使用响应式数据，很秀
        
        ![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/1b64792bf52e41379f9593ebd5571b1d~tplv-k3u1fbpfcp-zoom-1.image)
        
        ![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/becd95de70bc475c892802715d8e9994~tplv-k3u1fbpfcp-zoom-1.image)
        
*   devtools：同时支持两个版本，UI 优化和性能调试，等 vuex 支持后会比较实用。
    
    ![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/de28355d93164843ba6ac6296b47be30~tplv-k3u1fbpfcp-zoom-1.image)
    
    ![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/270c77cf89994691891b1253b97e0edc~tplv-k3u1fbpfcp-zoom-1.image)
    
*   更好的 IDE/TS 支持：Volar 会是一个非常实用的工具，可以提供模板中的代码提示功能，可提供 tsx 相同体验，终于等到你！
    
    ![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/ebaf3cbab57f44a6bd08fcfe0cea4709~tplv-k3u1fbpfcp-zoom-1.image)
    
*   vetur 替代者
    
    ![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/78a7fbb9b0c74c07b34781d79f69f561~tplv-k3u1fbpfcp-zoom-1.image)
    

### 兼容性相关

*   IE11 彻底被 vue3 抛弃
    
    ![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/3a37dd3a816d4d7691f2ddd232c64953~tplv-k3u1fbpfcp-zoom-1.image)
    
    vite+vue3 默认现代模式，即输出目标 ES6+，native ES modules；当然可以通过插件输出传统包。
    
    ![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/e9d7b1a8edac45debcdd52570d3df1eb~tplv-k3u1fbpfcp-zoom-1.image)
    
*   Vue3 迁移版：这是一个马甲版本，会是 vue3.1 的部分代码，披着 vue3 壳，实际是 vue2 内核，可以理解为用 vue3 编码方式写 vue2！
    
    ![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/7ae036ac70184d2f8f7f02731f9faae0~tplv-k3u1fbpfcp-zoom-1.image)Vue3.2：下一个小版本，主要最终确定 script setup 等 SFC 特性、Suspense 组件 / async setup、性能、ssr 等
    
    ![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/c5292270c4f44acf9e443f49e6d0e232~tplv-k3u1fbpfcp-zoom-1.image)
    
*   vue2.7：内置 composition api 的 vue2 版本
    
    ![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/099b6ed94f534190a47fae81302645e5~tplv-k3u1fbpfcp-zoom-1.image)
    
    vue3 迁移版就是 vue2.7？
    
    ![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/f8363405ec7144f3a8fe23ac56bc9d3f~tplv-k3u1fbpfcp-zoom-1.image)
    
*   vue3 将成为默认版本：vue2 将在 2021 年 Q2 成为过去时，默认将安装 v3
    
    ![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/a2dbdcd69f2e488c80329479311ae602~tplv-k3u1fbpfcp-zoom-1.image)