> 本文由 [简悦 SimpRead](http://ksria.com/simpread/) 转码， 原文地址 [segmentfault.com](https://segmentfault.com/a/1190000019622600)

> 日常开发中，我们都只专注在业务上的开发，拿起一套开箱即用的模板项目就直接开搞了，不知道大家有没有思考过，平时我们使用的脚手架里面到底做了什么，并且如果是自己来搭一套脚手架，应该怎么去搭呢？

前言
--

日常开发中，我们都只专注在业务上的开发，拿起一套开箱即用的模板项目就直接开搞了，不知道大家有没有思考过，平时我们使用的脚手架里面到底做了什么，并且如果是自己来搭一套脚手架，应该怎么去搭呢？

在本博客中，将记录作者搭建脚手架的过程，整体将分为两个部分，第一部分是 cli 的搭建，第二部分是模板项目的搭建；

cli 的搭建
-------

在搭建脚手架 cli 之前，我们首先思考一下，一个 cli，需要什么能力呢？答案是初始化能力，那么初始化的功能需要怎样去实现呢，这里，我们先梳理一下思路。

### 思路

我们在使用其他 cli 时，会发现，它们有问询的功能，比如询问项目名称，项目描述等；然后还可以选择模板项目将要使用什么 css 预处理器等的问题；所以，cli 首先就要具备**问询**功能，用以获取定制化信息；  
问询结束后，我们的 cli 会获得即将创建的项目的基本信息，接下来我们就需要以一个**_模板项目_**作为模板去创建，**_模板项目_**将会在第二部分讲解，这里一笔带过~；OK，既然有了模板项目，cli 就需要下载这个项目，然后复制这个项目，同时将前面问询所得的定制化信息写入项目配置中，所以 cli 还需要有**下载**和**复制，写入模板**功能；  
到此，一个模板项目基本已经创建成功了，那后面我们还希望脚手架可以帮忙进行 git 初始化以及安装依赖的功能，所以最终，还需要添加 **git 初始化**和**安装依赖**的功能；  
总结一下，实现一个 cli 的初始化功能我们需要有如下能力：  
**问询** ==> **下载模板** ==> **复制，写入模板** ==> **git 初始化** ==> **安装依赖**

### 准备

好了，为了实现上述思路，cli 将会引入如下依赖去实现对应的功能：

```
const program = require('commander');  // commander负责读取命令
const inquirer = require('inquirer');   // inquirer负责问询
const download = require('download-git-repo');   // download-git-repo负责下载对应模板项目的git仓库
const fse = require('fs-extra');   // fs-extra负责文件的复制
const memFs = require('mem-fs');
const editor = require('mem-fs-editor');   // mem-fs-editor负责模板的复制以及嵌入模板字符串，它需要依赖mem-fs
const { exec } = require('child_process');   // child_process负责执行命令行
```

当然除了上述必须的依赖外，为了更好的交互体验，还引入了如下依赖：

```
const chalk = require('chalk');   // 改变命令行输出样式
const ora = require('ora');   // 一个优雅地命令行交互spinner
```

至此，准备的工作已经完毕，下面，就开始一步步来编写 cli 了

cli 的编写
-------

万事开头难，有了思路后，实现思路才是真正的开始，下面将介绍如何组织 cli 项目

### 项目组织

首先看一下，工程的组织

```
├── bin                   
|   ├── begin
|   ├── begin-init 
├── src                   
|   ├── constants.js     
|   ├── project.js         
|   └── utils.js              
├── .gitignore                   
├── .npmrc
├── README.md
└── package.json
```

### 第一步，解析命令

在 **bin/begin** 中引入 **_commander_**，并且声明 init 命令，**_commander_** 会在同级目录中寻找 begin-init 文件

```
const program = require('commander');

program
  .usage('<command> [options]')
  .command('init [name]', 'init a project')
  .parse(process.argv);
```

### 第二步，问询

有了第一步的解析后，我们可以在 begin-init 文件中编写创建逻辑，这里，我们再抽象 project 文件到 src 目录底下，以便更好地分离职责，bin 文件只负责解析命令就好啦~  
下面就看看 project 文件中，如何实现问询

```
inquirer.prompt([{
      type: 'input',
      name: 'projectName',
      message: '请输入项目名：',
      validate(input) {
        if (!input) {
          return '项目名不能为空';
        }
        if (fse.existsSync(input)) {
          return '当前目录已存在同名项目，请更换项目名';
        }
        return true;
      }
    }]);
```

inquirer 提供 prompt 函数来实现问询，其参数为数组，问询顺序将按照数组的顺序来进行；

### 第三步，下载仓库

在问询结束后，我们基本知道需要创建的项目的名称、描述等信息了，下一步，就是下载仓库了，下面我们来看看核心代码：

```
const downloadPath = path.join(projectPath, '__download__');
download(TEMPLATE_GIT_REPO, downloadPath, { clone: true }, (err) => {
  // 拷贝
  // 此处省略若干代码
  // 拷贝完成后删除临时文件
  fse.remove(downloadPath);
}
```

这里需要注意的是，模板工程所在的仓库，即 **TEMPLATE_GIT_REPO**，最好是 public 的

### 第四步，复制文件，将信息写入模板

下载仓库只是把模板工程存放在一个临时文件夹内，真正的工程文件需要等信息写入模板后再生成；  
这里需要注意的是，模板写入时遵循 [ejs](https://link.segmentfault.com/?url=https%3A%2F%2Fejs.co%2F) 规范

```
const memFs = require('mem-fs');
const editor = require('mem-fs-editor');

// 这里需要mem-fs进行内存优化
const store = memFs.create();
this.memFsEditor = editor.create(store);

// 这里source表示源文件，dest表示目标文件，data表示需要写入的数据
this.memFsEditor.copyTpl(
  source,
  dest,
  data
);
```

在调用 copyTpl 时，data 字段中的 key-value 将被写入到模板中，举个栗子：

```
// 模板文件中，字段是这样的
{
  name: "<%= projectName %>"
}

// data就要这样去写
{
  projectName: 'this is your project name'
}
```

### 第五步，进行 git init 和 npm install

这两步的核心都是使用 nodejs 提供的 child_process 中 exec 方法去执行命令来实现，exec 能帮助我们在命令行中执行 shell 命令，exec 函数有对应的回调方法来让我们判断命令执行是否成功，核心代码如下：

```
exec('npm install', (error, stdout, stderr) => {
  if (error) {
    // 出错了
  } else {
    // 成功了
  }
})
```

是不是很简单呢，想了解更多的操作可以搜索官方文档查看哈，这里就不详解了。

调试和发布
-----

到这里，我们的 cli 已经实现的差不多了，这里我们总结一下如何测试和发布吧

```
npm link  // 本地调试
npm publish  // 发布
```

通过 npm link 命令，即可以在命令行工具测试你的 cli 了，注意在 **package.json** 的 **bin** 字段中定义好入口命令和文件

```
// package.json
{
  "bin": {
    "begin": "bin/begin"
  }
}
```

小结
--

OK，文章主要总结了编写一个脚手架中 init 功能的思路以及一些实现的方法，具体一些细节仍有优化的空间，欢迎大家讨论！  
文章的具体代码可以参考我的仓库 [begin-cli](https://link.segmentfault.com/?url=https%3A%2F%2Fgithub.com%2Fjackie-gan%2Fbegin-cli)