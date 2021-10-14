> 本文由 [简悦 SimpRead](http://ksria.com/simpread/) 转码， 原文地址 [segmentfault.com](https://segmentfault.com/a/1190000037612232)

> nodejs 交互工具库系列库作用 chalk-pipe 使用更简单的样式字符串创建粉笔样式方案 chalk 正确处理终端字符串样式 Commander.js 完整的 node.js 命令行解决方案 Inquirer.js 一组通用的交互式命令行用户界面。

nodejs 交互工具库系列
==============

<table><thead><tr><th>库</th><th>作用</th></tr></thead><tbody><tr><td>chalk-pipe</td><td>使用更简单的样式字符串创建粉笔样式方案</td></tr><tr><td>chalk</td><td>正确处理终端字符串样式</td></tr><tr><td>Commander.js</td><td>完整的 node.js 命令行解决方案</td></tr><tr><td>Inquirer.js</td><td>一组通用的交互式命令行用户界面。</td></tr><tr><td>slash</td><td>系统路径符处理</td></tr><tr><td>minimist</td><td>解析参数选项</td></tr><tr><td>dotenv</td><td>将环境变量从 .env 文件加载到 process.env 中</td></tr><tr><td>dotenv-expand</td><td>扩展计算机上已经存在的环境变量</td></tr><tr><td>hash-sum</td><td>非常快的唯一哈希生成器</td></tr><tr><td>deepmerge</td><td>深度合并两个或多个对象的可枚举属性。</td></tr><tr><td>yaml-front-matter</td><td>解析 yaml 或 json</td></tr><tr><td>resolve</td><td>实现 node 的 <code>require.resolve()</code>算法，这样就可以异步和同步地使用 require.resolve() 代表文件</td></tr><tr><td>semver</td><td>npm 的语义版本器</td></tr><tr><td>leven</td><td>测量两字符串之间的差异 &lt;br/&gt; 最快的 JS 实现之一</td></tr><tr><td>lru cache</td><td>删除最近最少使用的项的缓存对象</td></tr><tr><td>portfinder</td><td>自动寻找 <code>8000</code>至<code>65535</code>内可用端口号</td></tr><tr><td>ora</td><td>优雅的终端转轮</td></tr><tr><td>envinfo</td><td>生成故障排除软件问题 (如操作系统、二进制版本、浏览器、已安装语言等) 时所需的通用详细信息的报告</td></tr><tr><td>memfs</td><td>内存文件系统与 Node's fs API 相同实现</td></tr><tr><td>execa</td><td>针对人类的流程执行</td></tr><tr><td>webpack-merge</td><td>用于连接数组和合并对象，从而创建一个新对象</td></tr><tr><td>webpack-chain</td><td>使用链式 API 去生成简化 webpack 版本配置的修改</td></tr><tr><td>strip-ansi</td><td>从字符串中去掉 ANSI 转义码</td></tr><tr><td>address</td><td>获取当前机器的 IP, MAC 和 DNS 服务器。</td></tr><tr><td>default-gateway</td><td>通过对 OS 路由接口的 exec 调用获得机器的默认网关</td></tr><tr><td>joi</td><td>JavaScript 最强大的模式描述语言和数据验证器。</td></tr><tr><td>fs-extra</td><td>添加了未包含在原生<code>fs</code>模块中的文件系统方法，并向<code>fs</code>方法添加了 promise 支持</td></tr><tr><td>Acorn</td><td>一个小而快速的 JavaScript 解析器，完全用 JavaScript 编写。</td></tr><tr><td>zlib.js</td><td>ZLIB.js 是 ZLIB(RFC1950)， DEFLATE(RFC1951)， GZIP(RFC1952) 和 PKZIP 在 JavaScript 实现。</td></tr></tbody></table>

[nodejs 交互工具库 -- chalk-pipe 和 chalk](https://segmentfault.com/a/1190000037612232)

[nodejs 交互工具库 -- commander 和 Inquirer](https://segmentfault.com/a/1190000037629594)

[nodejs 交互工具库 -- slash, minimist 和 dotenv, dotenv-expand](https://segmentfault.com/a/1190000037645106)

[nodejs 交互工具库 -- hash-sum, deepmerge 和 yaml-front-matter](https://segmentfault.com/a/1190000037656240)

[nodejs 交互工具库 -- resolve 和 semver](https://segmentfault.com/a/1190000037662530)

[nodejs 交互工具库 -- leven, lru cache 和 portfinder](https://segmentfault.com/a/1190000037688124)

[nodejs 交互工具库 -- ora 和 envinfo](https://segmentfault.com/a/1190000037698795)

[nodejs 交互工具库 -- memfs 和 execa](https://segmentfault.com/a/1190000037702173)

[nodejs 交互工具库 -- webpack-merge 和 webpack-chain](https://segmentfault.com/a/1190000037778059)

[nodejs 交互工具库 -- strip-ansi, address, default-gateway 和 joi](https://segmentfault.com/a/1190000037794336)

[nodejs 交互工具库 -- fs-extra, Acorn 和 zlib](https://segmentfault.com/a/1190000038155823)

chalk-pipe
==========

使用更简单的样式字符串创建粉笔样式方案  
![](https://segmentfault.com/img/bVcHYLN)

Install
-------

```
yarn add chalk-pipe
```

Usage
-----

```
const chalkPipe = require('chalk-pipe');

console.log(chalkPipe('blue.bold')('Hello world!'));
```

使用点`.`区分多种样式:

```
const chalkPipe = require('chalk-pipe');

const link = chalkPipe('blue.underline');
const error = chalkPipe('bgRed.#cccccc');
const warning = chalkPipe('orange.bold');

console.log(link('Link!'));
console.log(error('Error!'));
console.log(warning('Warning!'));
```

![](https://segmentfault.com/img/bVcHYLO)

`chalkPipe` is also `chalk`:

```
const chalkPipe = require('chalk-pipe');
const blue = chalkPipe('blue');
const link = blue.underline;

console.log(link('Link!'));
```

使用定制的`chalk`

```
const chalk = require('chalk');
const chalkPipe = require('chalk-pipe');
const text =  chalkPipe('underline', chalk.blue)('Link!');

console.log(text);
```

API
---

### chalkPipe(styles)(text)

```
chalkPipe('blue.underline')('Link!');
```

### chalkPipe(styles, chalk)(text)

```
const chalk = require('chalk');

chalk.enable = true;

chalkPipe('underline', chalk.blue)('Link!');
```

Valid styles
------------

*   [Modifiers](https://link.segmentfault.com/?enc=DMDEkxBZtmS9OYxzBFzJTg%3D%3D.MDemy3N0wv9tymmnz0cTE8EiGG1shvv0sRoPxsRlOBp1K3V7I%2BXTncAlS2MaBlBB)
*   [Colors](https://link.segmentfault.com/?enc=sVvK%2BCvkIk6DitHrdRXRNQ%3D%3D.abKL6DBCXeYZVu3YWYUwlYcWv4%2FSttht4kznW5s5%2B6M%2BZUGz4n2glKgEjoR7nrUD)
*   [Background colors](https://link.segmentfault.com/?enc=4VUbUSdx%2BPUwUy1d3XE3vg%3D%3D.qBZHUf9uNggeQUYQJC7%2FnZi8jlDIzz3D2%2BszuW7%2FQFI38Hh86PhgHEUF3YTDVFZEv23Gvpx6lGqyyfhZ3e%2B0OQ%3D%3D)
*   [Hex triplet](https://link.segmentfault.com/?enc=Nx6T2iRsD8wcqnJ6JfMJMQ%3D%3D.a2CSTksL4puWuCdiNaJhvlRCslOS4pXdYDUcRzYKEmGFGxcjp6MeZAhBBEkFaHFD1OeSxzCGw%2BaGi5yvX3km9A%3D%3D)
*   [CSS keywords](https://link.segmentfault.com/?enc=agf2uhBwM0ZlwaBTKkLIBw%3D%3D.xt%2BuHnajz%2FWEq4zhRJDT5jxr7h%2F%2BI40bI%2FWuC%2FEr%2F8rGKkaix1Tx8Fzs%2FtEYvzkJcDx5l8CHhelSD%2FyvKFyKCw%3D%3D)

参考
--

基本常用的方法场景就这些了, 更完整的用法可以直接查阅文档

[chalk-pipe](https://link.segmentfault.com/?enc=gSoyKcOHR4Pu3bD9zC7VEA%3D%3D.h%2FrFbS5j0%2FcyBG4%2F5JhwGahnDk%2Fmog0pE4H8HaXhNRnQm6r1LdWcIf9FibX6rhuV)

如果需要更加细致的需求, 就要使用下面的`chalk`了, 毕竟这只是它简化版的库

chalk
=====

![](https://segmentfault.com/img/bVcHYOY)

> 正确处理终端字符串样式

![](https://segmentfault.com/img/bVcHYOZ)

亮点
--

*   富有表现力的 API
*   高性能
*   嵌套样式的能力
*   256 / 真彩颜色支持
*   自动侦测颜色支持
*   不延长 String.prototype
*   清洁和集中
*   积极维护
*   截至 2020 年 1 月 1 日，被约 5 万个软件包使用

Install
-------

```
yarn add chalk
```

Usage
-----

Chalk 提供了一个易于使用的可组合 API，您只需链嵌您想要的样式。

```
const chalk = require('chalk');
const log = console.log;

// Combine styled and normal strings
log(chalk.blue('Hello') + ' World' + chalk.red('!'));

// Compose multiple styles using the chainable API
log(chalk.blue.bgRed.bold('Hello world!'));

// Pass in multiple arguments
log(chalk.blue('Hello', 'World!', 'Foo', 'bar', 'biz', 'baz'));

// Nest styles
log(chalk.red('Hello', chalk.underline.bgBlue('world') + '!'));

// Nest styles of the same type even (color, underline, background)
log(chalk.green(
  'I am a green line ' +
  chalk.blue.underline.bold('with a blue substring') +
  ' that becomes green again!'
));

// ES2015 template literal
log(`
  CPU: ${chalk.red('90%')}
  RAM: ${chalk.green('40%')}
  DISK: ${chalk.yellow('70%')}
`);

// ES2015 tagged template literal
log(chalk`
  CPU: {red 20%}
  RAM: {green 30%}
  DISK: {rgb(255,131,0) 40%}
`);

// Use RGB colors in terminal emulators that support it.
log(chalk.keyword('orange')('Yay for orange colored text!'));
log(chalk.rgb(123, 45, 67).underline('Underlined reddish color'));
log(chalk.hex('#DEADED').bold('Bold gray!'));
```

![](https://segmentfault.com/img/bVcHYO0)

API
---

### chalk.`<style>[.<style>...](string, [string...])`

Example: `chalk.red.bold.underline('Hello', 'world');`

链样式，并将最后一个作为带有字符串参数的方法调用。顺序不重要，以后的样式在发生冲突的情况下会有先例. 这仅仅意味着`chalk.red.yellow.green`等价于 `chalk.green`.

多个参数将用空格分隔。

### chalk.level

指定颜色支持的级别。

颜色支持是自动检测到的，但是您可以通过设置 level 属性来覆盖它。不过，您应该只在自己的代码中这样做，因为它将全局应用于所有 chalk 使用者。

如果您需要在可重用模块中更改此内容，则创建一个新实例:

```
const ctx = new chalk.Instance({level: 0});
```

<table><thead><tr><th>Level</th><th>Description</th></tr></thead><tbody><tr><td><code>0</code></td><td>All colors disabled</td></tr><tr><td><code>1</code></td><td>Basic color support (16 colors)</td></tr><tr><td><code>2</code></td><td>256 color support</td></tr><tr><td><code>3</code></td><td>Truecolor support (16 million colors)</td></tr></tbody></table>

### chalk.supportsColor

检测终端是否支持颜色。内部使用，为您处理，但为了方便暴露。

可以由用户使用标志`--color`和`--no-color`覆盖。对于不可能使用`--color`的情况，使用环境变量 `FORCE_COLOR=1`(级别 1)、`FORCE_COLOR=2`(级别 2) 或`FORCE_COLOR=3`(级别 3) 强制启用 color，或`FORCE_COLOR=0`强制禁用。使用`FORCE_COLOR`会覆盖所有其他颜色支持检查。

显式 256/Truecolor 模式可分别使用`--color=256`和`--color=16m`标志启用。

### chalk.stderr and chalk.stderr.supportsColor

`chalk.stderr`包含一个单独的实例，该实例配置了针对 `stderr`流而不是`stdout`检测到的颜色支持. 重写规则`chalk.supportsColor`也适用于此,`chalk.stderr.supportsColor`为了方便暴露

Styles
------

### Modifiers

*   `reset` - 重置当前颜色链。
*   `bold` - 加粗文本。
*   `dim` - 只发出少量的光。
*   `italic` - 使文本斜体。(不是广泛支持)
*   `underline` - 使文本下划线。(不是广泛支持)
*   `inverse`- 背景色和前景色反转。
*   `hidden` - 打印文本，但使其不可见。
*   `strikethrough` - 在文本中心放置一条水平线。(不是广泛支持)
*   `visible`- 仅当粉笔的颜色级别为 > 时打印文本。对于纯粹修饰的东西很有用。

### Colors

*   `black`
*   `red`
*   `green`
*   `yellow`
*   `blue`
*   `magenta`
*   `cyan`
*   `white`
*   `blackBright` (alias: `gray`, `grey`)
*   `redBright`
*   `greenBright`
*   `yellowBright`
*   `blueBright`
*   `magentaBright`
*   `cyanBright`
*   `whiteBright`

### Background colors

*   `bgBlack`
*   `bgRed`
*   `bgGreen`
*   `bgYellow`
*   `bgBlue`
*   `bgMagenta`
*   `bgCyan`
*   `bgWhite`
*   `bgBlackBright` (alias: `bgGray`, `bgGrey`)
*   `bgRedBright`
*   `bgGreenBright`
*   `bgYellowBright`
*   `bgBlueBright`
*   `bgMagentaBright`
*   `bgCyanBright`
*   `bgWhiteBright`

Tagged template literal
-----------------------

Chalk 可以用作已标记的模板文字

```
const chalk = require('chalk');

const miles = 18;
const calculateFeet = miles => miles * 5280;

console.log(chalk`
    There are {bold 5280 feet} in a mile.
    In {bold ${miles} miles}, there are {green.bold ${calculateFeet(miles)} feet}.
`);
```

块由左花括号 ({)、样式、一些内容和右花括号(}) 分隔。

模板样式与普通 Chalk 样式完全相同。以下三句话是等价的:

```
console.log(chalk.bold.rgb(10, 100, 200)('Hello!'));
console.log(chalk.bold.rgb(10, 100, 200)`Hello!`);
console.log(chalk`{bold.rgb(10,100,200) Hello!}`);
```

注意函数样式 (`rgb()`, `hsl()`, `keyword()`, 等等.) 参数之间可能不包含空格

所有插入值 (chalk\`${foo}\`) 通过 `.toString()`方法转换为字符串, 内插值字符串中的所有花括号 ({和}) 都要转义。

Browser support
---------------

从 Chrome 69 开始，ANSI 转义码就在开发者控制台得到了本地支持。

Windows
-------

如果你使用的是 Windows，帮你自己一个忙，使用 Windows 终端而不是 cmd.exe。

参考
--

基本常用的方法场景就这些了, 更完整的用法可以直接查阅文档

[chalk](https://link.segmentfault.com/?enc=o4A4KyG6DfnnqINt4ehPfQ%3D%3D.fsi%2B%2BMlNDw7w901jqADK1trDVYobBkLRO1Muc5LuGYQ%3D)

supports-color
==============

> 检测终端是否支持颜色

Install
-------

```
yarn add supports-color
```

Usage
-----

```
const supportsColor = require('supports-color');

if (supportsColor.stdout) {
    console.log('Terminal stdout supports color');
}

if (supportsColor.stdout.has256) {
    console.log('Terminal stdout supports 256 colors');
}

if (supportsColor.stderr.has16m) {
    console.log('Terminal stderr supports 16 million colors (truecolor)');
}
```

API
---

返回一个带有`stdout`和`stderr`属性的对象，用于测试这两个流。每个属性都是一个对象，如果不支持颜色，则为 false。

`stdout`/`stderr` 对象通过一个`.level`属性和一个对应的标志来指定对颜色的支持程度:

*   `.level = 1` and `.hasBasic = true`: Basic color support (16 colors)
*   `.level = 2` and `.has256 = true`: 256 color support
*   `.level = 3` and `.has16m = true`: Truecolor support (16 million colors)

参考
--

基本常用的方法场景就这些了, 更完整的用法可以直接查阅文档

[supports-color](https://link.segmentfault.com/?enc=MPU5HXn6FP0fxfRO1o%2Fvvg%3D%3D.djPJJFO19EBqEzAayGCOWSS5gdojh4L6L3QX6lnWxUnF71r95kgVOGcIcstOaUU8)