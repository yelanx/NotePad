> 本文由 [简悦 SimpRead](http://ksria.com/simpread/) 转码， 原文地址 [blog.csdn.net](https://blog.csdn.net/qq_35630674/article/details/104359636#t12)

不难，但是很广，很多知识要深究，基本上会从你自己说的技术里面出问题，所以说项目用到了什么技术一定要深究一下技术。

目前只记得这些。对手写代码的考察还挺有要求的。

**目录**

[1、解释原型链和原型对象](#t0)

[2、websocket 和 http2 了解吗解释一下（大学学过不记得了）socket 是什么？](#t1)

[（1）websocket](#t2)

[（2）socket，说到 websocket 我觉得有必要说下跟 socket 的区别。](#t3)

[（3）HTTP2.0](#t4)

[3、自己实现一个 event 类](#t5)

[4、自己实现 new 函数](#t6)

[5、网络七层协议](#t7)

[6、TCP 和 IP 分别属于哪一层? TCP 和 UDP 的区别? 分别适用于什么情况?](#t8)

[7、对公司有什么了解（我觉得可能最好说一下业务，我乱说的然后面试官笑了）](#t9)

[8、自我介绍](#t10)

[9、在项目里有挑战的东西或者学到了什么（谨慎回答，题目大多从这里出）](#t11)

[10、vue 组件通信怎么实现 父子和不父子](#t12)

[方法一、props/$emit 父子之间](#t13)

[方法二、$emit/$on](#t14)

[方法三、vuex](#t15)

[方法四、$attrs/$listeners](#t16)

[方法五、provide/inject](#t17)

[方法六、$parent / $children 与 ref](#t18)

[11、vuex 的 mutation 和 action 的区别，为什么要设立两个而不是直接都用 mutation 操作 state](#t19)

[12、只给一个 div，无外层容器信息，让他水平垂直居中](#t20)

[13、css 选择器优先级排序（记得要全面的一个不漏记住）](#t21)

[14、应用层有哪些协议，用到了 UDP 的有哪些？](#t22)

[15、自己实现一个防抖函数 （以防万一节流的也学一下吧）](#t23)

[16、sort() 是内部使用了什么算法 时间复杂度是多少 indexOf() 的时间复杂度是多少](#t24)

[18、有没有了解过 typeScript](#t25)

[19、webpack](#t26)

1、解释原型链和原型对象
============

在 Javascript 中，当系统加载构造函数后，会自动在内存中生成一个对象，这个对象就是原型对象。两者之间在内存中表现为相对独立，不存在谁包含谁的关系。但是两者之间又有一些关联，在构造函数的内部存在一个 prototype 属性指向原型对象，同时在原型对象的内存也存在一个属性 constructor 其指向了构造函数。

原型链就是当构造函数实例化的对象访问一个不存在的属性或方法，系统会自动到当前构造器所指向的原型对象中去寻找，找到则直接使用。

![](https://img-blog.csdnimg.cn/20200218125019325.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM1NjMwNjc0,size_16,color_FFFFFF,t_70)

![](https://img-blog.csdnimg.cn/20200218134325293.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM1NjMwNjc0,size_16,color_FFFFFF,t_70)

2、websocket 和 http2 了解吗解释一下（大学学过不记得了）socket 是什么？
================================================

（1）websocket
------------

WebSocket 是 HTML5 开始提供的一种在单个 TCP 连接上进行**全双工通讯**的协议。

WebSocket 使得客户端和服务器之间的数据交换变得更加简单，允许服务端主动向客户端推送数据。在 WebSocket API 中，浏览器和服务器只需要完成一次握手，两者之间就直接可以创建持久性的连接，并进行双向数据传输。

在 WebSocket API 中，浏览器和服务器只需要做一个握手的动作，然后，浏览器和服务器之间就形成了一条快速通道。两者之间就直接可以数据互相传送。

**现在，很多网站为了实现推送技术，所用的技术都是 Ajax 轮询。轮询是在特定的的时间间隔（如每 1 秒），由浏览器对服务器发出 HTTP 请求，然后由服务器返回最新的数据给客户端的浏览器。这种传统的模式带来很明显的缺点，即浏览器需要不断的向服务器发出请求，然而 HTTP 请求可能包含较长的头部，其中真正有效的数据可能只是很小的一部分，显然这样会浪费很多的带宽等资源。HTML5 定义的 WebSocket 协议，能更好的节省服务器资源和带宽，并且能够更实时地进行通讯。**

浏览器通过 JavaScript 向服务器发出建立 WebSocket 连接的请求，连接建立以后，客户端和服务器端就可以通过 TCP 连接直接交换数据。

当你获取 Web Socket 连接后，你可以通过 **send()** 方法来向服务器发送数据，并通过 **onmessage** 事件来接收服务器返回的数据。

```
class EventEmitter {
    constructor () {
        super()
        this.events = {}
        this.onceEvents = {} 
    }
 
    on (type, cb) {
        if (!type || !cb) {
            return false
        }
        this.events[type] = this.events[type] || []
        this.events[type].push(cb)
    }
     
    emit (type) {
        if (!type) {
            return false
        }
        this.events[type] && this.events[type].forEach(eventCb => {
            eventCb.apply(this, [...arguments].slice(1))
        })
        this.onceEvents[type] && this.onceEvents[type].forEach(eventCb => {
            eventCb.apply(this, [...arguments].slice(1))
        })
        delete this.onceEvents[type]
    }
 
    off (type) {
        if (!type) {
            return false
        }
        delete this.events[type]
        delete this.onceEvents[type]
    }
 
    once (type, cb) {
        if (!type || !cb) {
            return false
        }
        this.onceEvents[type] = this.onceEvents[type] || []
        this.onceEvents[type].push(cb)
    }
}
```

以上代码中的第一个参数 url, 指定连接的 URL。第二个参数 protocol 是可选的，指定了可接受的子协议。

[https://www.runoob.com/html/html5-websocket.html](https://www.runoob.com/html/html5-websocket.html)

（2）socket，说到 websocket 我觉得有必要说下跟 socket 的区别。
--------------------------------------------

软件通信有七层结构，下三层结构偏向与数据通信，上三层更偏向于数据处理，中间的传输层则是连接上三层与下三层之间的桥梁，每一层都做不同的工作，上层协议依赖与下层协议。基于这个通信结构的概念。

**Socket 其实并不是一个协议，是应用层与 TCP/IP 协议族通信的中间软件抽象层，它是一组接口。**当两台主机通信时，让 Socket 去组织数据，以符合指定的协议。TCP 连接则更依靠于底层的 IP 协议，IP 协议的连接则依赖于链路层等更低层次。

WebSocket 则是一个典型的应用层协议。

在计算机通信领域，socket 被翻译为 “套接字”，它是计算机之间进行**通信**的**一种约定**或一种方式。通过 socket 这种约定，一台计算机可以接收其他计算机的数据，也可以向其他计算机发送数据

（3）HTTP2.0
----------

**HTTP 的基本优化**

影响一个 HTTP 网络请求的因素主要有两个：**带宽和延迟。**

*   **带宽：**如果说我们还停留在拨号上网的阶段，带宽可能会成为一个比较严重影响请求的问题，但是现在网络基础建设已经使得带宽得到极大的提升，我们不再会担心由带宽而影响网速，那么就只剩下延迟了。
    
*   **延迟：**
    
    *   浏览器阻塞（HOL blocking）：浏览器会因为一些原因阻塞请求。浏览器对于同一个域名，同时只能有 4 个连接（这个根据浏览器内核不同可能会有所差异），超过浏览器最大连接数限制，后续请求就会被阻塞。
        
    *   DNS 查询（DNS Lookup）：浏览器需要知道目标服务器的 IP 才能建立连接。将域名解析为 IP 的这个系统就是 DNS。这个通常可以利用 DNS 缓存结果来达到减少这个时间的目的。
        
    *   建立连接（Initial connection）：HTTP 是基于 TCP 协议的，浏览器最快也要在第三次握手时才能捎带 HTTP 请求报文，达到真正的建立连接，**但是这些连接无法复用会导致每次请求都经历三次握手和慢启动**。三次握手在高延迟的场景下影响较明显，慢启动则对文件类大请求影响较大。
        

SPDY（读作 “SPeeDY”）是 [Google](https://baike.baidu.com/item/Google) 开发的基于 [TCP](https://baike.baidu.com/item/TCP/33012) 的会话层 [1]  协议，用以最小化网络延迟，提升网络速度，优化用户的网络使用体验。SPDY 并不是一种用于替代 HTTP 的协议，而是对 [HTTP](https://baike.baidu.com/item/HTTP) 协议的增强。新协议的功能包括数据流的多路复用、请求优先级以及 HTTP 报头压缩。谷歌表示，引入 SPDY 协议后，在实验室测试中页面加载速度比原先快 64%。

**HTTP2.0 和 HTTP1.X 相比的新特性**

*   **新的二进制格式**（Binary Format），HTTP1.x 的解析是基于文本。基于文本协议的格式解析存在天然缺陷，文本的表现形式有多样性，要做到健壮性考虑的场景必然很多，二进制则不同，只认 0 和 1 的组合。基于这种考虑 HTTP2.0 的协议解析决定采用二进制格式，实现方便且健壮。
    
*   **多路复用**（MultiPlexing），即连接共享，即每一个 request 都是是用作连接共享机制的。一个 request 对应一个 id，这样一个连接上可以有多个 request，每个连接的 request 可以随机的混杂在一起，接收方可以根据 request 的 id 将 request 再归属到各自不同的服务端请求里面。
    
*   **header 压缩**，如上文中所言，对前面提到过 HTTP1.x 的 header 带有大量信息，而且每次都要重复发送，HTTP2.0 使用 encoder 来减少需要传输的 header 大小，通讯双方各自 cache 一份 header fields 表，既避免了重复 header 的传输，又减小了需要传输的大小。
    
*   **服务端推送**（server push），同 SPDY 一样，HTTP2.0 也具有 server push 功能。
    

**HTTP2.0 的升级改造**

*   前文说了 HTTP2.0 其实可以支持非 HTTPS 的，但是现在主流的浏览器像 chrome，firefox 表示还是只支持基于 TLS 部署的 HTTP2.0 协议，所以要想升级成 HTTP2.0 还是先升级 HTTPS 为好。
    
*   当你的网站已经升级 HTTPS 之后，那么升级 HTTP2.0 就简单很多，如果你使用 NGINX，只要在配置文件中启动相应的协议就可以了，可以参考 **NGINX 白皮书，NGINX 配置 HTTP2.0 官方指南** https://www.nginx.com/blog/nginx-1-9-5/。
    
*   使用了 HTTP2.0 那么，原本的 HTTP1.x 怎么办，这个问题其实不用担心，HTTP2.0 完全兼容 HTTP1.x 的语义，对于不支持 HTTP2.0 的浏览器，NGINX 会自动向下兼容的。
    

3、自己实现一个 event 类
================

class Event {

    on(name, cb)

    once(name, cb)

    emit(name, message)

    off(name, cb)

}

```
function newOperator(ctor){
                if(typeof ctor !== 'function'){
                  throw 'newOperator function the first param must be a function';
                }
                newOperator.target = ctor;
                var newObj = Object.create(ctor.prototype);
                var argsArr = [].slice.call(arguments, 1);
                var ctorReturnResult = ctor.apply(newObj, argsArr);
                var isObject = typeof ctorReturnResult === 'object' && ctorReturnResult !== null;
                var isFunction = typeof ctorReturnResult === 'function';
                if(isObject || isFunction){
                    return ctorReturnResult;
                }
                return newObj;
            }
            
            // es6版本
            function _new(fn, ...arg) {
                const obj = Object.create(fn.prototype);
                const ret = fn.apply(obj, arg);
                return ret instanceof Object ? ret : obj;
            }
```

4、自己实现 new 函数
=============

function A() {}

let a = new A();.

```
JSON.stringify(state.subscribeList);   // array -> string
JSON.parse(window.localStorage.getItem("subscribeList"));    // string -> array
```

5、网络七层协议
========

6、TCP 和 IP 分别属于哪一层? TCP 和 UDP 的区别? 分别适用于什么情况?
=============================================

TCP/UDP 传输层，IP 网络层，

1、连接方面区别

TCP [面向连接](https://www.baidu.com/s?wd=%E9%9D%A2%E5%90%91%E8%BF%9E%E6%8E%A5&tn=SE_PcZhidaonwhc_ngpagmjz&rsv_dl=gh_pc_zhidao)（如打电话要先拨号建立连接）。

UDP 是无连接的，即发送数据之前不需要建立连接。

2、安全方面的区别

TCP 提供可靠的服务，通过 TCP 连接传送的数据，无差错，不丢失，不重复，且按序到达。

UDP 尽最大努力交付，即不保证可靠交付。

3、传输效率的区别

TCP 传输效率相对较低。

UDP 传输效率高，适用于对高速传输和实时性有较高的通信或广播通信。

4、连接对象数量的区别

TCP 连接只能是点到点、一对一的。

UDP 支持一对一，一对多，多对一和多对多的交互通信。

TCP 协议需要三次握手通信成功后进行建立，应用场景：互联网和企业网上客户端应用，数据传输性能让位于数据传输的完整性，可控制性和可靠性。  
UDP 协议是直接发送，不会判断是否接收和发送成功，应用场景：当强调输出性能而非完整性时，如音频和多媒体的应用。

7、对公司有什么了解（我觉得可能最好说一下业务，我乱说的然后面试官笑了）
====================================

8、自我介绍
======

9、在项目里有挑战的东西或者学到了什么（谨慎回答，题目大多从这里出）
==================================

10、vue 组件通信怎么实现 父子和不父子
======================

方法一、`props`/`$emit父子之间`
-----------------------

方法二、`$emit`/`$on`
-----------------

**这种方法通过一个空的 Vue 实例作为中央事件总线（事件中心），用它来触发事件和监听事件, 巧妙而轻量地实现了任何组件间的通信，包括父子、兄弟、跨级**。当我们的项目比较大时，可以选择更好的状态管理解决方案 vuex。

方法三、vuex
--------

Vuex 实现了一个单向数据流，在全局拥有一个 State 存放数据，当组件要更改 State 中的数据时，必须通过 Mutation 进行，Mutation 同时提供了订阅者模式供外部插件调用获取 State 数据的更新。而当所有异步操作 (常见于调用后端接口异步获取更新数据) 或批量的同步操作需要走 Action，但 Action 也是无法直接修改 State 的，还是需要通过 Mutation 来修改 State 的数据。最后，根据 State 的变化，渲染到视图上。

Mutation 中是同步函数和 Action 中是异步函数，属于一种约定，为了更好的追踪 vuex 的状态变化，希望我们都按照这种原则进行开发

Mutation 必须是同步函数

更改 state 的唯一方法是通过提交 mutation

Action 中不进行状态的直接更改，而是通过 commit 触发 mutation

mutation 的触发通过 store.commit 来进行

action 的触发通过 store.dispatch 进行

异步操作的条件依赖第一个异步操作的结果。

第二个异步操作的条件依赖第一个异步操作的结果，比如第一个异步操作执行了 count ++ ，count :0 = >1，第二个异步操作会先判断当前 count 的值，if(count === 1) { do something...} else { do something... }

 当异步操作涉及互相依赖的情况的时候，我们肯定希望被依赖的操作执行完成之后，再执行依赖项，这样能保证程序执行得到正确的结果，但异步操作，如接口访问这种，往往是不能确定执行完成的时间的。

state 利用 Vue 的细粒度数据响应机制来进行高效的状态更新。

由于 vuex 里，我们保存的状态，都是数组，而 localStorage 只支持字符串，所以需要用 JSON 转换。

```
JSON.stringify(state.subscribeList);   // array -> string
JSON.parse(window.localStorage.getItem("subscribeList"));    // string -> array
```

方法四、`$attrs`/`$listeners`
-------------------------

多级组件嵌套需要传递数据时，通常使用的方法是通过 vuex。但如果仅仅是传递数据，而不做中间处理，使用 vuex 处理，未免有点大材小用。

*   `$attrs`：包含了父作用域中不被 prop 所识别 (且获取) 的特性绑定 (class 和 style 除外)。当一个组件没有声明任何 prop 时，这里会包含所有父作用域的绑定 (class 和 style 除外)，并且可以通过 v-bind="$attrs" 传入内部组件。通常配合 interitAttrs 选项一起使用。
*   `$listeners`：包含了父作用域中的 (不含 .native 修饰器的) v-on 事件监听器。它可以通过 v-on="$listeners" 传入内部组件

方法五、provide/inject
------------------

Vue2.2.0 新增 API, 这对选项需要一起使用，**以允许一个祖先组件向其所有子孙后代注入一个依赖，不论组件层次有多深，并在起上下游关系成立的时间里始终生效**。一言而蔽之：祖先组件中通过 provider 来提供变量，然后在子孙组件中通过 inject 来注入变量。  
**provide / inject API 主要解决了跨级组件间的通信问题，不过它的使用场景，主要是子组件获取上级组件的状态，跨级组件间建立了一种主动提供与依赖注入的关系**。

方法六、`$parent` / `$children`与 `ref`
----------------------------------

*   `ref`：如果在普通的 DOM 元素上使用，引用指向的就是 DOM 元素；如果用在子组件上，引用就指向组件实例
*   `$parent` / `$children`：访问父 / 子实例

[https://segmentfault.com/a/1190000019208626](https://segmentfault.com/a/1190000019208626)

11、vuex 的 mutation 和 action 的区别，为什么要设立两个而不是直接都用 mutation 操作 state
=================================================================

异步操作的条件依赖第一个异步操作的结果。

第二个异步操作的条件依赖第一个异步操作的结果，比如第一个异步操作执行了 count ++ ，count :0 = >1，第二个异步操作会先判断当前 count 的值，if(count === 1) { do something...} else { do something... }

 当异步操作涉及互相依赖的情况的时候，我们肯定希望被依赖的操作执行完成之后，再执行依赖项，这样能保证程序执行得到正确的结果，但异步操作，如接口访问这种，往往是不能确定执行完成的时间的。

12、只给一个 div，无外层容器信息，让他水平垂直居中
============================

<div></div>

position:relative;

top:50%;

left:50%;

transform: translate(-50%,-50%)

怎么实现的，含义。

transform 的 translate 是基于什么点?

scale(1) scale(2) scale(0.5) 分别代表什么，是基于什么点？

CSS 盒模型是基于哪个点?

左上角的点。

13、css 选择器优先级排序（记得要全面的一个不漏记住）
=============================

浏览器默认属性 < 继承自父元素的属性（比如 div color 是 333 下层的 a 也是 333） < 通配符选择器（ 通配选择器用一个星号（*）表示。） < 标签选择器 < 类选择器 < 结构伪类选择器 < id 选择器 < 行内样式 < !important

**结构伪类：**

![](https://img-blog.csdnimg.cn/20200218142029304.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM1NjMwNjc0,size_16,color_FFFFFF,t_70)

14、应用层有哪些协议，用到了 UDP 的有哪些？
=========================

应用层最经典的就是 DNS 和 HTTP 了。

应用层协议如下：

(1) 域名系统 (Domain Name System，DNS)：用于实现网络设备名字到 IP 地址映射的网络服务。

(2) [文件传输](https://www.baidu.com/s?wd=%E6%96%87%E4%BB%B6%E4%BC%A0%E8%BE%93&tn=SE_PcZhidaonwhc_ngpagmjz&rsv_dl=gh_pc_zhidao)协议 (File Transfer Protocol，FTP)：用于实现交互式[文件传输](https://www.baidu.com/s?wd=%E6%96%87%E4%BB%B6%E4%BC%A0%E8%BE%93&tn=SE_PcZhidaonwhc_ngpagmjz&rsv_dl=gh_pc_zhidao)功能。

(3) 简单邮件传送协议 (Simple Mail Transfer Protocol, SMTP)：用于实现电子邮箱传送功能。

(4) 超文本传输协议 (HyperText Transfer Protocol，HTTP)：用于实现 WWW 服务。

(5) 简单网络管理协议 (simple Network Management Protocol，SNMP)：用于管理与监视网络设备。

(6) [远程登录](https://www.baidu.com/s?wd=%E8%BF%9C%E7%A8%8B%E7%99%BB%E5%BD%95&tn=SE_PcZhidaonwhc_ngpagmjz&rsv_dl=gh_pc_zhidao)协议 (Telnet)：用于实现[远程登录](https://www.baidu.com/s?wd=%E8%BF%9C%E7%A8%8B%E7%99%BB%E5%BD%95&tn=SE_PcZhidaonwhc_ngpagmjz&rsv_dl=gh_pc_zhidao)功能。

DNS 用的 UDP, 其他一些 P2P 应用, 如迅雷, BT 等也会用 UDP 来传输。（其中 DNS 既可以基于 TCP，也可以基于 UDP。）

一般用的都是 TCP, 比如 HTTP, SSL ,FTP, SMTP, POP3, IMAP 等等很多。

15、自己实现一个防抖函数 （以防万一节流的也学一下吧）
============================

16、sort() 是内部使用了什么算法 时间复杂度是多少 indexOf() 的时间复杂度是多少
=================================================

快排     O (nlogn)

indexOf()   O (n)

17、下列代码的输出是什么

function Dog(name, color) {

    this.name = name

    this.color = color

    this.bark = function() {

        console.log(`hello ${this.name}`)

    }

}

Dog.prototype.bark = function () {

    console.log(`hi ${this.name}`)

}

let dog = new Dog('Chop', 'Red');

dog.bark();

dog.__proto__.bark.call(dog);

hello Chop // 本身的方法

hi Chop // 原型的方法

18、有没有了解过 typeScript
====================

19、webpack
==========

  WebPack 可以看做是模块打包机：它做的事情是，分析你的项目结构，找到 JavaScript 模块以及其它的一些浏览器不能直接运行的拓展语言（Scss，TypeScript 等），并将其打包为合适的格式以供浏览器使用

**入口起点 (entry point)** 指示 webpack 应该使用哪个模块，来作为构建其内部_依赖图_的开始。进入入口起点后，webpack 会找出有哪些模块和库是入口起点（直接和间接）依赖的。

每个依赖项随即被处理，最后输出到称之为 _bundles_ 的文件中。