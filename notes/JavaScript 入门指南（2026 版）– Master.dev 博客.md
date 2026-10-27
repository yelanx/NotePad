> 本文由 [简悦 SimpRead](http://ksria.com/simpread/) 转码， 原文地址 [blog.master.dev](https://blog.master.dev/what-to-know-in-javascript-2026-edition/)

> An overview of what's new in language features, frameworks, runtimes, build tools, testing, and more.......

We’ve done [posts like this for CSS](https://frontendmasters.com/blog/what-you-need-to-know-about-modern-css-2025-edition/), but JavaScript deserves the same dangnabit! Especially as JavaScript does a better job of versioning itself anyway. We’ll cover new stuff in the language itself, but being a JavaScript practitioner involves more than the language itself, extending into runtimes, frameworks, libraries, and tooling. Let’s just do this, you’ve probably scrolled down already anyway.

[](#whats-new-in-the-language)What’s New in the Language
--------------------------------------------------------

JavaScript has yearly version releases, which is a pretty nice way of doing things if you ask me!

### [](#ecmascript-2025)ECMAScript 2025

The latest is **ECMAScript 2025,** which came out in June 2025, and [the whole spec of that version is available](https://tc39.es/ecma262/2025/).

#### [](#iterator-helpers)Iterator Helpers

There are now methods like `.map()`, `.filter()`, `.take()`, `.drop()` directly on iterators with lazy evaluation. Honestly, to a mostly front-end guy like me, this feels a bit esoteric. Like, we already can map over arrays, so what’s the big deal? But I do understand performance, and that’s one aspect here.

```
const result = array
  .map(x => x * 2)      // creates a new array in memory
  .filter(x => x > 10)  // ... and again
  .slice(0, 3);         // ... and again
```

So that’s “slow” and “memory intensive”, especially if the array is quite large and the things you’re doing are “expensive” as they say. The fancy new way is like this:

```
const result = Iterator.from(array)
  .map(x => x * 2)
  .filter(x => x > 10)
  .take(3)
  .toArray(); // No new arrays created, computation stops after 3
```

And as a nice bonus, that whole `Iterator.from()` thing works on anything iterable. So not just arrays, but sets, maps, generators, etc, which means they all get the same nice set of functions to use.

#### [](#set-methods)Set Methods

Sets are kinda nice in JavaScript as it’s like an array only each item is guaranteed to be unique. That’s nothing new, but if you have _two_ sets, now we have methods for returning interesting things about them, like what overlaps, what doesn’t, etc.

```
const youKnow  = new Set(["JS", "Python", "CSS", "SQL"]);
const jobNeeds  = new Set(["JS", "TypeScript", "Python"]);

// Skills the job wants that you already have
youKnow.intersection(jobNeeds); // → Set {"JS", "Python"}

// Everything combined — your full stack + job needs
youKnow.union(jobNeeds); // → Set {"JS", "Python", "CSS", "SQL", "TypeScript"}

// What the job needs that you DON'T know yet (skill gaps)
jobNeeds.difference(youKnow); // → Set {"TypeScript"}

// Skills you have that the job doesn't care about
youKnow.difference(jobNeeds); // → Set {"CSS", "SQL"}

// Skills that appear in only one set, not both
youKnow.symmetricDifference(jobNeeds); // → Set {"CSS", "SQL", "TypeScript"}

// Are all job requirements a subset of what you know?
jobNeeds.isSubsetOf(youKnow); // → false

// Do you have every skill and more?
youKnow.isSupersetOf(jobNeeds); // → false

// Do you and the job have zero overlap?
youKnow.isDisjointFrom(jobNeeds); // → false
```

Pretty useful, I’d say. Claude Code had fun [producing an interactive demo](https://codepen.io/editor/chriscoyier/pen/019c9733-c5ed-7594-9bd9-8ed47f3860d1?file=%2Findex.html&orientation=left&show=preview) from that.

#### [](#regex-updates)RegEx Updates

Lemme set the stage here. You’re building an on-page search function where your users type in their own search terms. And you want to implement this as a RegEx search. There is some danger there, as some characters a user may type in are “special” characters in RegExs, like how a $ matches the last character or whatever. So if the user searches for $9 and you just dunk that into a RegEx, it would break. Which characters you need to “escape” to fix that are specific to the implementation of RegEx at hand.

So! After apparently a 15-year journey, there is now `RegExp.escape()`.

```
const query = userInput; // 例如 "$5.00 (off!)" 

// ❌ BEFORE — 会因正则表达式中的特殊字符而报错
const badRe = new  RegExp (query, "g" ); // ✅ ES2025 — 一个方法，问题解决const goodRe = new RegExp ( RegExp .escape(query), "g" );


 
```

克劳德·科德再次[凭借一个相当不错的演示大获成功](https://codepen.io/editor/chriscoyier/pen/019cbeea-b5e2-7af0-acbe-96dab95e0ee8)。

正则表达式中“标志”的使用方式也进行了更新。我觉得最常用的标志是“i”，它表示不区分大小写。所以，如果你有一个以“i”结尾的正则表达式，就`/i`表示整个表达式都不区分大小写。但如果你只想让正则表达式的_一部分_不区分大小写呢？现在你可以将这部分用括号括起来，并在开头添加相应的标志。

```
// 旧方法——无法混合区分大小写
/[az]+@[AZ]+/i   // 'i' 标志应用于所有内容

// ES2025——内联修饰符按组划分
/(?i:[az.]+)@(?-i:[AZ]+)\.(?i:com|org)/ //^^^^ 不区分大小写部分// ^^^^^ 区分大小写部分// ^^^ 不区分大小写部分



```

#### [](#promise-update)承诺更新

大家最喜欢的异步编程流程模型（Promise）迎来了一些更新，`Promise.try()`可以简化错误处理。一个函数可能会以同步_或_异步方式出错，以前你需要分别处理它们，但现在你可以一起处理了：

```
// 一个可能异步的函数，可能会抛出同步错误
function  loadUser ( id ) {
   if (!id) throw  new  Error ( "没有 ID" );           // 同步抛出
  错误 return fetch( `/api/users/ ${id} ` );            // 异步
} // ❌ 之前 — 两条独立的错误路径let p;
 try { 
  p = loadUser(id);                            // 在这里捕获同步抛出的错误… 
} catch (e) { handleError(e); } 
p?.catch( e => handleError(e));                 // …以及异步拒绝错误// ✅ ES2025 — 一行代码，一个 .catch() Promise .try( () => loadUser(id)) 
  .then( user   => render(user)) 
  .catch( err   => showError(err));              // 同时捕获两者






```

我再次推荐您观看[Claude Code 制作的演示视频](https://codepen.io/editor/chriscoyier/pen/019cc8dc-9bde-7597-9b57-d91d6ef0a6c2)，该视频在演示这一概念方面做得非常出色。

#### [](#import-attributes)导入属性

[导入属性](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/import/with)是我个人非常喜欢的功能。真的！首先，我喜欢直接导入 JSON 数据，而不用先获取、解析等等：

```
从 “./file.json” 导入数据，使用{ type : 'json' }
```

后面那部分`with`对象就是所谓的“导入属性”，它们还有一些其他的技巧，我们稍后会讲到。

JSON 导入对我来说看起来不错，而且还能节省一两行代码，但公平地说，它也有一些非常明显的缺点，Jake Archibald 在[《导入与获取 JSON》一](https://jakearchibald.com/2025/importing-vs-fetching-json/)文中指出了这一点。其中一个很大的缺点是：如果导入失败，“它会导致整个模块图崩溃”，这非常糟糕。你可以使用动态方法`import()`来应对`catch`失败……

```
try {
   const { default : data } = await import(url, { 
    with: { type: 'json' }, 
  }); 
} catch (error) {
   // 回退逻辑
}
```

但这不如直接使用`fetch`JSON 数据处理工具获取的数据好，所以效果仍然一般。Jake 补充道，导入的数据“会在页面生命周期内一直存在于模块图中”，而不是像使用 `get_modules` 处理后的数据那样会被垃圾回收`fetch`。总之：谨慎行事。

不过，JSON并不是唯一可以使用导入属性导入的文件类型。我说导入属性是我个人最喜欢的功能之一，主要是指我很高兴能用这种方式导入CSS。

```
import componentStyles from  "./component.css"  with { type : "css" };
```

[我在《使用 Web 组件和 CSS 模块脚本构建简洁原生应用架构》一文](https://frontendmasters.com/blog/architecture-through-component-colocation/)中详细探讨了这一点。我非常喜欢这种将 CSS 代码保留在 CSS 文件中的方式，这些文件可以放在与 JavaScript 组件相邻的文件夹中。

```
import sheet from  './styles.css'  with { type : 'css' }; class MyComponent extends HTMLElement {
   constructor () {
     super ();
     const shadowRoot = this .attachShadow({ mode : 'open' }); 
    shadowRoot.adoptedStyleSheets = [sheet]; 
  } 
  ...

    

```

这并非 ES2025 的全部内容，还有很多其他文章专门探讨了这些内容。我发现 Matthew Tyson为 InfoWorld 撰写的[《ECMAScript 2025：JavaScript 的最佳新特性》这篇文章](https://www.infoworld.com/article/4021944/ecmascript-2025-the-best-new-features-in-javascript.html)很有帮助。例如，它包含一些关于内存使用方面的信息`Float16Array`，这有点超出我的专业范围，但它涉及在确定需要时如何权衡精度和内存使用。

### [](#ecmascript-2026-expected-mid-2026)ECMAScript 2026（预计 2026 年年中）

现在距离2026年初还有一段时间，但我们预计ECMAScript年度版本将像往年一样在年中发布。以下是一些已经进入第四阶段并可能包含在最终版本中的功能。

#### [](#temporal-api)临时 API

这绝对是 JavaScript 近来最令人兴奋和实用的新功能。简单来说就是“JavaScript 的日期和时间功能现在非常完善，无需任何库”。长期以来，像[Moment](https://momentjs.com/)这样功能强大但体积庞大的库填补了这一空白，迫使开发者在性能和用户体验之间做出选择😬。

在我撰写本文时，Safari 是最后一个不 [支持的浏览](https://caniuse.com/temporal)器，但它一直在 [努力改进](https://blogs.igalia.com/compilers/2026/01/31/implementing-the-temporal-proposal-in-javascriptcore/)，现在已经进入 TP（他们称之为“技术预览”）阶段，所以距离正式发布并不遥远。

现在获取特定时区的时间非常简单，无需任何库。

```
const now = Temporal.Now.zonedDateTimeISO( "America/New_York" );
 // 程序化日期
console.log ( now.toString ()); // 或者更易读... 
console.log (now.toLocaleString ( ));


```

TC39 会议的设置让我眼前一亮，[它包含一段代码，可以在 DevTools 控制台中运行](https://github.com/tc39/agendas/blob/main/2026/03.md)，显示即将召开的会议在你当前时区的时间：

```
Temporal.ZonedDateTime.from ( '2026-03-10T10:00[America/New_York]' ). withTimeZone 
  (Temporal.Now.timeZoneId()) // 您的时区
  .toLocaleString();
```

太酷了。

Temporal 能做的事情数不胜数，但以下这些事情以前真的很糟糕。

就像如果我们把一月份的最后一天“加上一个月”，就会得到一个非常奇怪的结果：

```
const date = new  Date ( 2026 , 0 , 31 ); // 1月31日
date.setMonth(date.getMonth() + 1 ); // "加一个月" 
console.log (date.toDateString()); // 2026年3月3日 星期日 ❌ 😬
```

但是有了好用的 Temporal API，我们就束手无策了：

```
const jan31 = Temporal.PlainDate.from ( "2026-01-31" ) ;
 const feb = jan31.add ( { months: 1 }); 
console.log(feb.toString()); // 2026-02-28 ✅
```

而且，现在比较事物是……正确的。

```
const a = Temporal.Duration.from ( { hours: 25 });
 const b = Temporal.Duration.from ( { days: 1 }); const cmp = Temporal.Duration.compare(a, b, { relativeTo: Temporal.Now.plainDateISO() }); 
console.log(cmp); // 1 (25 小时 > 1 天) ✅


```

#### [](#explicit-resource-management)显式资源管理

在编写异步函数和使用 await 时，新增了一个`using`关键字，用于确保清理工作。运行时保证，当变量超出作用域时，会调用`[Symbol.dispose]()``await` 或 ` await` 方法。`[Symbol.asyncDispose]()`

```
class  FileHandle  {
   constructor (path) {
     this .path = path;
     console .log( `已打开${path} ` );
  } async write(data) {
     // ... 写入数据
  } async [ Symbol .asyncDispose]() {
     await someFlushOperation();
     console .log( `已刷新并关闭${ this .path} ` );
  }
} async function saveData (

  

  

  { await
   using file = new FileHandle( "output.txt" );
   await file.write( "hello world" );
   // 即使抛出错误，文件也会在此处自动刷新并关闭
}
```

对于单个资源来说，关键字`using`很好用，但现在也有了 DisposableStack，用于多个资源，你需要确保清理它们。

```
异步 函数 runJob ({ await using stack = new AsyncDisposableStack(); const db = stack.use( await openDatabase());
   const file = stack.use( new FileHandle( "output.txt" ));
   const tmpDir
   = stack.defer( async () => removeTempDir( "/tmp/job" )); // 执行操作... await file.write( await db.query( "SELECT * FROM jobs" )); // 即使抛出异常，这三个文件也会按相反的顺序在这里被清理掉
}

  

  
  

  
```

#### [](#array-fromasync-iterator-sequencing)`Array.fromAsync`/ 迭代器序列

`Array.fromAsync`它最初计划在 2024 年发布，但显然存在一些规范问题，因此直到 ES2026 才被纳入规范。它会在遍历异步迭代器时**等待每个`yield`ed 值**，并将结果收集到一个普通数组中。如果没有它，你就必须手动循环并推送元素。

```
异步 函数* fetchNumbers () {
   yield  1 ;
   await  new  Promise ( r => setTimeout(r, 100 )); // 模拟异步延迟
  yield  2 ;
   await  new  Promise ( r => setTimeout(r, 100 ));
   yield  3 ; 
} const numbers = await Array.fromAsync (fetchNumbers());
 console.log (numbers); // [1, 2, 3]

 
```

当你在等待一个循环遍历多个异步函数并等待它们返回结果的函数调用时，例如分页之类的操作，这种方法可能最为有用。除了使用`yield``ing` 之外，你还可以传入一个 Promise 数组，该数组会在所有 Promise 都解析完成后返回结果。

说到分页，`Iterator.concat`它新增了一个功能，允许你对正在迭代的每个元素进行延迟求值。因此，它无需预先将所有内容都放入数组中进行迭代，而是可以执行迭代，但如果你提前终止迭代，就可以节省原本用于填充数组的内存。

```
const page1 = [ { id: 1 }, { id: 2 } ][ Symbol.iterator ](); 
const page2 = [ { id: 3 }, { id: 4 } ][ Symbol.iterator ](); 
const page3 = [ { id: 5 }, { id: 6 } ][ Symbol.iterator ](); 

for (const item of Iterator.concat(page1, page2, page3)) { 
  process(item); // 延迟处理所有页面
}
```

#### [](#error-iserror)`Error.isError()`

关键在于，现在你可以可靠地判断一个值是否是真正的`Error`对象，而不仅仅是看起来像对象的东西。这在集中式错误报告服务等场景中非常有用，因为这类服务可能会从 Web Worker 或 iframe 等不同的“领域”接收错误，而这些领域可能会导致服务出错。

#### [](#math-sumprecise)`Math.sumPrecise`

你肯定已经看到了`console.log(0.1 + 0.2);`——结果非常奇怪`0.30000000000000004`。说来话长。好吧，你试试看`console.log(Math.sumPrecise([0.1, 0.2]));`——（目前Firefox浏览器支持此功能），你会发现……完全一样。

但显然[它](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Math/sumPrecise)在某些方面仍然有用🤷‍♀️

#### [](#base64-hex-encoding)Base64/十六进制编码

现在有了一种简单直接的方法来调用这些东西，这真是太好了。

```
const val = "前端主控！" ;
 const textEnc = new TextEncoder();
 const bytes = textEnc.encode(val); 
console.log ( bytes.toBase64());
 // 'RnJvbnRlbmQgTWFzdGVycyE=' 
console.log (bytes.toHex());
 // '46726f6e74656e64204d61737465727321'
```

再一次，让 Claude Code[用一种非常巧妙的方式演示一下](https://codepen.io/editor/chriscoyier/pen/019d5093-782b-7ab5-a507-f689e9e00173)。

[](#new-in-frameworks)框架新增功能
----------------------------

### [](#react-ecosystem)React 生态系统

[React 19](https://react.dev/blog/2024/12/05/react-19)于 2024 年 12 月发布，至今已有一段时间了。我们现在使用的是 19.2 版本，据我所知，关于 React 20 的内容还没有太多公开信息。

但 React 19 版本更新幅度相当大，引入了所谓的[“RSC”（React 服务器组件）](https://react.dev/reference/rsc/server-components)、[React 编译器](https://react.dev/learn/react-compiler)和[服务器端操作](https://18.react.dev/reference/rsc/server-actions)。简而言之：

*   **RSC**：如果可以使用 Node 服务器， _或许_ 可以省略一些通常会打包到客户端 JavaScript 中的组件，而将这些工作改为在服务器端完成，只将所需的数据传回服务器。
*   **服务器操作：**说到拥有一个可用的 Node 服务器，这些操作允许您调用服务器上特有的函数。表单处理就是一个典型的例子。
*   **编译器**：一些性能优化传统上需要人工来完成。你是这方面的`useMemo`专家吗？我也不是。但如果你先用这个编译器运行你的 React 代码，它就能帮你完成这些优化。只需增加一点构建复杂度，就能获得一点性能提升。

当然，还有很多其他的小细节，但总的来说，这些是你应该了解的重要事项。React [Native 已经发布了 0.83 版本](https://reactnative.dev/blog/2025/12/10/react-native-0.83)，很遗憾，我对这个版本了解甚少，但我确实觉得他们“宣布”（算是）发布了 1.0 版本这一点很值得关注，这对于所有参与开发的人来说，在经历了十年的开发之后，想必都是一件令人欣喜的事情。我找不到相关的链接，我记得这个消息是在 React Universe Conf 大会上以现场宣布的形式发布的。

那些刚刚问世的服务器端 React 技术？嗯，它们去年接连遭遇了[非常严重的安全漏洞，这理所当然地吓坏了很多人。](https://react.dev/blog/2025/12/03/critical-security-vulnerability-in-react-server-components)

### [](#vue-ecosystem)Vue 生态系统

Vue 3.5 保持稳定，而 [Vue 3.6 已进入 alpha测试阶段，新增了一项名为](https://github.com/vuejs/core/releases/tag/v3.6.0-alpha.1)[Vapor Mode 的](https://github.com/vuejs/core/releases/tag/v3.6.0-alpha.1#about-vapor-mode) 可选功能  ，可大幅提升性能（“与 Solid 和 Svelte 5 相当”）。

2024 年我们[对整个 Vue 生态系统有了很好的概览](https://frontendmasters.com/blog/the-vue-ecosystem-in-2024/)。但作为一个完全的 Vue 外行，我很难理解 2025/2026 年的发展趋势。显然，Evan You 是这方面的主要负责人，但他运营着 [VoidZero](https://voidzero.dev/)  （“JavaScript 工具公司”），该公司现在推出了 [Vite+](https://viteplus.dev/)，其中包含 _一系列_ 重要项目，例如 Vite 本身、格式化、代码检查、测试等等。这些都不是 Vue 特有的，我猜想在所有这些事情同时进行的情况下，很难专注于 Vue 本身🤷‍♀️。

可以说，Vue 的主要元框架是 Nuxt，[它已经发布了 4.0 版本](https://nuxt.com/blog/v4)。Nuxt本身的“管理者”是 NuxtLabs，该公司已被 [Vercel 收购](https://vercel.com/blog/nuxtlabs-joins-vercel)。所以 Vercel 并不“拥有”Nuxt，但……某种程度上来说是这样？一方面，我很高兴元框架在理论上拥有可持续发展的归宿；另一方面，我又觉得很奇怪，VoidZero 几乎拥有 JavaScript 工具链中的所有组件，唯独缺少一个来自其母语的元框架。Pinia 似乎是 Vue 的主要状态管理库，而 Vue[已经发布了 v3 版本](https://pinia.vuejs.org/cookbook/migration-v2-v3.html)并停止了对 Vue 2 的支持。

### [](#svelte-ecosystem)Svelte 生态系统

Svelte 已经[更新到 v5 版本了](https://svelte.dev/blog/svelte-5-is-alive)。这是 Svelte 世界的一个重大更新，引入了所谓的“Runes API”，彻底改变了响应式编程的工作方式，使其更加“细粒度”，正如他们所说，这意味着效率更高、速度更快。说实话，我对 Svelte 和 [SvelteKit](https://svelte.dev/docs/kit/introduction)了解不多，只知道它们也是 Vercel 的一部分，而且深受用户喜爱。

[](#javascript-runtimes)**JavaScript 运行时**
------------------------------------------

最主流的运行时环境显然是那些内置于浏览器中的。但就你可以选择并自行运行的运行时环境而言，Node 仍然是主导者，不过也出现了两个有趣的竞争对手。[我们之前讨论过 Deno 或 Bun 何时可以成为](https://frontendmasters.com/blog/when-deno-or-bun-is-a-better-solution-than-node-js/)Node 的可行替代方案。最近，这些运行时环境之间的融合趋势大于分歧，它们都原生支持 TypeScript，并且对标准的 Node 版本提供了更多支持。

### [](#node-js)Node.js

最近Node领域最大的新闻或许就是它可以[原生运行TypeScript文件了](https://nodejs.org/en/learn/typescript/run-natively)。所以：

```
运行 my-script.ts脚本
```

从 Node 22.18.0 开始，无需`--experimental-strip-types`再添加标志即可正常工作。但请注意，它仍然会移除_类型_信息，这意味着它不会在 TypeScript 代码存在实际问题时发出警告。

Node 领域最大的新闻往往是一些简单但重要的基础性问题，例如安全性、性能和与浏览器 JavaScript API 的一致性方面的改进。

就我个人而言，我对 Node 的发展非常满意。我参与过一些项目，这些项目都切换到了 Node 内置的测试运行器，这大大减少了依赖项，感觉很棒。我赞赏 Node 在[权限模型](https://nodejs.org/api/permissions.html#permissions)方面的改进，这使得它在处理不受信任的代码时更加易用。

### [](#bun)包子

[Bun 的重大版本是 1.3，](https://bun.com/blog/bun-v1.3)`bun`其中包含许多围绕运行开发服务器的 DX 功能。只需指向 HTML 文件即可运行功能齐全的开发服务器，这确实令人满意：

```
包 './**/*.html'
```

Bun 还负责所有的处理和打包工作，因此从某种程度上来说，它成为了 Vite 的替代品。

对Bun来说，或许最大的新闻是[Anthropic（例如Claude）](https://www.anthropic.com/news/anthropic-acquires-bun-as-claude-code-reaches-usd1b-milestone)去年底收购了Bun。我认为普遍的看法是，这对Bun来说是个好消息，它拥有了一个稳定且资金充足的归宿。

通常，人们选择 Bun 是因为速度快。它通过 npm 安装速度极快，而且整体性能也更快。[但代价是牺牲了一些稳定性](https://js-segfault-compare.sigmasd.workers.dev/)。

### [](#deno)迪诺

Deno 已经[发布 v2 版本](https://deno.com/blog/v2.0)一段时间了。据我所知，它完全兼容 Node.js，并且是这三个库中最稳定的。由于`npm:`包名中的指定符，它现在也完全兼容 npm。

我认为人们选择 Deno 的主要原因在于其稳定性和安全性优先的架构。[他们对此表达得非常明确：](https://docs.deno.com/runtime/fundamentals/security/)

> Deno 默认是安全的。除非您明确启用，否则使用 Deno 运行的程序无法访问敏感 API，例如文件系统访问、网络连接或环境变量访问。您必须使用命令行标志或运行时权限提示显式授予这些资源的访问权限。这与 Node 有很大不同，Node 的依赖项会自动获得对所有系统 I/O 的完全访问权限，这可能会给您的项目引入潜在的安全漏洞。

这是很好的设计。

[](#build-tools)构建工具
--------------------

### [](#vite)维特

[Vite](https://vite.dev/)已成为 JavaScript 生态系统中主流的构建工具。我想，它真是生逢其时！虽然 Vite 出自 Vue 的同一团队之手，但它几乎适用于任何前端项目。我非常欣赏他们的开发理念：本地开发只需更新代码中因工作而产生的少量变更，无需进行完整的打包；同时，它还能按需生成生产级打包文件。

[Vite 最近升级到了 v8 版本](https://vite.dev/blog/announcing-vite8)。这是一个重大变化，它不再依赖第三方打包工具[Rollup](https://rollupjs.org/)，而是使用Vite 自主开发的打包工具[Rolldown](https://rolldown.rs/)。这与 Vite 近期致力于打造更“统一的工具链”的战略相符，正如他们所说。他们可以在旗下产品中共享工具（例如解析器），从而使整个流程更加可预测。他们将这套工具链称为[Vite+](https://viteplus.dev/)，其中包括 Vite 的高级开发服务器、格式化、代码检查、类型检查、测试、任务运行、单体仓库支持和打包功能。功能非常强大！

他们甚至正在努力更进一步，开发一个名为 [Void 的“部署平台”，](https://void.cloud/) 该平台利用 Cloudflare 提供的托管、数据存储、云功能等服务。

> 数据库、键值存储、对象存储、AI推理、身份验证、队列和定时任务，全部内置。导入所需功能，跳过不需要的功能。

如今几乎所有框架都在使用 Vite：[Astro](https://astro.build/)、[SolidStart](https://start.solidjs.com/)、[SvelteKit](https://svelte.dev/docs/kit/introduction)、[Nuxt](https://nuxt.com/)等等。值得注意的是 Next.js，它使用 webpack，并且正在迁移到 Turbopack（参见下一节）。但我们甚至看到[Cloudflare 将 Next.js 的 AI 功能移植到了 Vite](https://github.com/cloudflare/vinext)，这一举动引发了争议。

### [](#turbopack)涡轮增压器

[Turbopack](https://nextjs.org/docs/app/api-reference/turbopack)是 Vercel 开发的打包工具，从 Next.js v16 开始已成为[默认打包工具](https://nextjs.org/docs/app/guides/upgrading/version-16#turbopack-by-default)。Turbopack 是一个基于 Rust 的项目，其刷新速度据称比之前 Next.js 版本中的 webpack 快 5-10 倍。目前，Turbopack 似乎仅适用于 Next.js。

### [](#webpack)webpack

[webpack](https://webpack.js.org/)仍然被广泛使用，并且制定了[2026 年的发展计划](https://webpack.js.org/blog/2026-02-04-roadmap-2026/)，其中包括许多减少对各种加载器的需求和其他简化措施的想法。这是一个值得欢迎的更新，因为人们普遍认为 webpack 过于复杂。

[](#typescript)TypeScript
-------------------------

[TypeScript 刚刚发布了 v6 版本](https://devblogs.microsoft.com/typescript/announcing-typescript-6-0/)。他们表示 v7 版本将于 2026 年年中发布，这将是一个重大版本更新，会切换到基于 Go 的新编译器。v6 的主要目的是进行一些必要的调整，为这次变更做好准备。我认为 Bytes 新闻简报中有[一个很好的简要总结](https://bytes.dev/archives/473)：

> 严格模式现在 `true` 默认启用， `module` 默认值为 `esnext`， `target` 浮动值遵循当前版本的 ES 规范（目前为 `es2025`），并且 `types` 现在默认使用空数组而不是清空数组中的所有内容 `node_modules/@types`。仅最后一点就会导致很多项目出现问题，但应该也能提升 20% 到 50% 的速度。

或许应该为 v7 版本做好准备，因为你几乎肯定会想要体验到 VS Code 和 Playwright 等软件中约 10 倍的速度提升。

值得注意的是，TypeScript 已成为[GitHub 上排名第一的语言](https://github.blog/news-insights/octoverse/octoverse-a-new-developer-joins-github-every-second-as-ai-leads-typescript-to-1/)，同比增长 66%。

### [](#types-in-javascript)JavaScript 中的类型？

[几年前曾有人讨论](https://devblogs.microsoft.com/typescript/a-proposal-for-type-syntax-in-javascript/)过直接在 JavaScript 中添加类型定义。这样或许就能在无需编译器的情况下获得 TypeScript 的一些优势。但目前看来，这项提议进展缓慢，不太可能真正取代 TypeScript 的功能。

### [](#ai)人工智能

现在或许是插入这段话的最佳时机，鉴于 TypeScript 的极度流行——无论是在开发者的积极使用中，还是在可供语言学习模型 (LLM) 训练的开源项目中——人工智能如今在编写代码方面，尤其是 TypeScript，都表现出色。据称，92% 的开发者都在某种程度上使用人工智能来编写代码，这是一个惊人的增长，也是目前开发领域最引人瞩目的话题。

[](#testing)测试
--------------

所有主流的 JavaScript 测试框架依然存在，并承担着大部分的测试工作，例如 Jest、Jasmine、Mocha 等。但近年来也出现了一些变化，尤其是随着 Vite 的迅速崛起，其测试框架[Vitest](https://vitest.dev/)也开始流行起来。Vitest 与 Jest 兼容，因此将测试移植到 Vitest 上通常非常容易，而且速度更快（在我看来，界面也更美观）。Vitest 还具有“浏览器模式”，这意味着它可以在真实的浏览器中运行测试，这对于测试组件至关重要。Playwright 也具备类似的功能[，](https://playwright.dev/)并且似乎正在迅速发展，它本身就可以进行“端到端”测试，而且其受欢迎程度似乎已经超过了[Puppeter](https://pptr.dev/)或[Cypress](https://www.cypress.io/)（至少在我看来是这样）。

[](#meta-frameworks)元框架
-----------------------

### [](#next-js)Next.js

[Next.js 目前版本为 v16，](https://nextjs.org/blog/next-16)这是第一个默认使用 Turbopack 的版本。我个人很喜欢这种进步，但由于迁移过程比较困难，我在自己的项目中已经禁用了 Turbopack。不过，日志记录和错误处理的改进确实是一个显著的进步。这个版本的 Next.js 会自动使用 React 编译器和 React 服务器组件，理论上可以全面提升性能，但实际结果似乎[更加](https://www.developerway.com/posts/bundle-size-investigation) [复杂](https://javascript.plainenglish.io/are-react-server-components-improving-your-apps-b4afdd6d5196) [且](https://medium.com/@dan0dev/react-server-components-the-good-the-bad-the-ugly-e08e63b8e676) [参差不齐](https://dev.to/rbobr/the-hidden-performance-costs-of-react-server-components-248f)。

如果你经常在 Next.js 网站上使用 AI，那么值得注意的是，[他们现在推出了 MCP 服务器。](https://nextjs.org/docs/app/guides/mcp)这意味着，如果你将它连接起来，AI 在处理你的网站时将会更加智能。

它也支持 React 19，这意味着它得到`<ViewTransition>`了支持，[我们在这里对此进行了研究](https://frontendmasters.com/blog/reacts-viewtransition-element/)。

### [](#remix-react-router)Remix / React Router

几年前，[Remix](https://remix.run/)被 Shopify 收购。它推出了 v2 版本，之后又宣布原本的 Remix v3 实际上只是[React Router](https://reactrouter.com/) v7。现在 Remix v3 仍然存在，但目前仍在[积极开发中](https://remix.run/blog/wake-up-remix)。最重要的是，React 将不再是其中的一部分：

> 相反，我们正在构建我们自己的组件模型，它比我们以前见过的任何模型都更接近 Web。

[他们举办了一场名为 Remix Jam 的](https://remix.run/jam/2025)活动，他们在活动中进行了很多尝试，如果你对此非常感兴趣，可以去看看。

### [](#tanstack)坦斯塔克

Remix 事件带来的一些混乱可能对[TanStack 生态系统](https://tanstack.com/)有所裨益，TanStack 是一系列工具的集合，其中包括[一个非常流行的路由器](https://tanstack.com/router/latest)。与之前的 Remix 一样，这个路由器也发展成了[一个框架。](https://tanstack.com/start/latest)

我们从 Adam Rackis 那里获得了[大量关于 TanStack 世界的内容。](https://frontendmasters.com/blog/tag/tanstack/)

### [](#astro)星体

[Astro](https://astro.build/)多年来一直保持强劲发展势头，并且没有放慢脚步。就在今年，他们被 [Cloudflare 收购](https://blog.cloudflare.com/astro-joins-cloudflare/)，这通常被认为是一件好事，因为真正优秀的前端框架很难围绕其构建强大的商业模式，而解决方案似乎是与专业的托管服务商合作。目前，它已经被用于[构建一个奇特的 WordPress 克隆网站](https://blog.cloudflare.com/emdash-wordpress/)。

如果你想构建一个默认静态的网站，但仍然使用现代 JavaScript 框架的组件化架构，并且可以轻松选择加入更动态的行为，那么 Astro 就是黄金标准，在我看来也是一个非常好的选择。

[Astro 的最新版本是 6.0](https://astro.build/blog/astro-6/)，它包含了一些更成熟的功能，例如在开发过程中自定义运行时、内容安全策略以及一个实验性的更快编译器。紧随其后的是[6.1 版本](https://astro.build/blog/astro-610/)，其中包含许多实用的小改进，这证明了他们致力于打造优秀框架的决心。

[](#npm)**npm**
---------------

[npm](https://www.npmjs.com/)领域似乎并没有发生太多大事。自从微软/GitHub 收购它以来已经过去了六年，它看起来运行良好。而 GitHub 本身却一直[面临着正常运行时间的问题](https://damrnelson.github.io/github-historical-uptime/)。

npm更令人_担忧_的是供应链安全事件，例如[s1ngularity 事件](https://nx.dev/blog/s1ngularity-postmortem)，该事件窃取了用户的凭证、令牌和配置文件，并将其公开发布在 GitHub 上😳。此外，还有[debug/chalk](https://www.wiz.io/blog/widespread-npm-supply-chain-attack-breaking-down-impact-scope-across-debug-chalk)事件，恶意软件包更新可能会将加密货币交易重定向到某些不法分子的钱包。然后是 Shai-Hulud 蠕虫（抱歉， 是多个_蠕虫_），这是一种能够自我复制的凭证窃取病毒，其 2.0 版本会覆盖/删除用户主目录中的所有文件。该病毒感染了 796 个 npm 软件包，下载量超过 2000 万次，真是……令人震惊。从安全角度来看，npm 去年可谓是祸不单行。

如果你有使用 npm 的重要生产应用程序，那么为了获得一些保护，或许值得考虑使用像[Socket这样的工具。](https://socket.dev/)

[](#what-should-i-learn)我应该学些什么？
--------------------------------

永恒的答案是：学习这些技术的基本原理，无论工具、框架等等如何变化，都将使你受益匪浅。而且，恕我直言，人工智能在代码编写方面发挥的作用越大，我们就越需要像你这样真正了解自己在做什么的人，能够帮助我们规划、指导、完善、测试、架构代码，并赋予代码良好的设计理念，无论代码是如何创建的。

[报名参加前端大师课程](https://frontendmasters.com/courses/?utm_source=boost&utm_medium=blog&utm_campaign=boost)是掌握这些基本技能的途径。