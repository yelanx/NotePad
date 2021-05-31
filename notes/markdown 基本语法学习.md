> 本文由 [简悦 SimpRead](http://ksria.com/simpread/) 转码， 原文地址 [www.52pojie.cn](https://www.52pojie.cn/thread-1449716-1-1.html)

> [md]# markdown 基本语法 ## 一、前言 - ** 由于部分语法无法展示，推荐使用 Typora 解锁全套，下载地址：https://www.typora.io/**- **markdown 数学公式大......

 ![](https://www.52pojie.cn/uc_server/images/noavatar_middle.gif) 淘小欣

markdown 基本语法
=============

一、前言
----

*   **由于部分语法无法展示，推荐使用 Typora 解锁全套，下载地址：[https://www.typora.io/](https://www.typora.io/)**
*   **markdown 数学公式大全，[https://www.cnblogs.com/nickchen121/p/11746655.html](https://www.cnblogs.com/nickchen121/p/11746655.html)**

基本介绍：

Markdown 是一种可以使用普通文本编辑器编写的标记语言，通过简单的标记语法，它可以使普通文本内容具有一定的格式。

如果一直使用`word`撰写文档，有比较大的弊病：

*   只能对纯文本文件进行版本控制，而 word 是二进制格式
*   格式繁杂，经常需要中断写作来控制格式
*   代码与文档分离，给写文档造成更大的阻力

而假如单纯的使用 txt，就没有一点格式了，用 html 虽然既有格式又能加入版本控制，但是需要花费较多的时间在标签上，而且标签占了文档的较大百分比，不易阅读。

> 所以，最终的解决方案就是 **Markdown** ，作为一种轻量级的标记语言，能在非常短的时间内掌握。而且不仅可以用于写文档，还可以写博客、写简书、做一些随手笔记。Markdown 文件以. md 结尾，可以导出为 html 和 PDF（需要使用的工具支持）。
> 
> 它是一种语法（个人认为是简化版的 html），但是和 html 不同的是，各种解析器对其会有不同的表现。比如我的 IDEA Markdown 插件会把分割线显示成一条细灰线，Cmd Markdown 则是显示一条虚线。所以建议保持使用一种 Markdown 工具和尽量使用标准语法。

二、Markdown 基本语法
===============

2.1 标题
------

代码：

```
# 一级标题
## 二级标题
### 三级标题
#### 四级标题
##### 五级标题
###### 最小只有六级标题
```

效果：

自己复制粘贴便可知晓！

2.2 加粗
------

代码：

```
**我被加粗了**
```

效果：

**我被加粗了**

2.3 斜体
------

代码：

```
*我倾斜了了*
```

效果：

_我倾斜了了_

2.4 高亮
------

代码：

```
==我高亮了==
```

效果：

== 我高亮了 ==

2.5 上标
------

代码：

```
2^2^
```

效果：

22

2.6 下标
------

代码：

```
H~2~o
```

效果：

H2o

2.7 代码引用（> 式）
-------------

代码：

```
> hello markdown!
```

效果：

> hello markdown!

代码：

```
> hello markdown!
>> hello markdown!
```

效果：

> hello markdown!
> 
> > hello markdown!

2.8 代码引用（``` 式）
---------------

代码：

```
# 不要复制这一句话，并且去掉下面的反斜杠，谢谢！
\`\`\`python
print('hello nick')
\`\`\`
```

效果：

```
print('hello nick')
```

2.9 代码引入（` 式）
-------------

代码：

```
# 不要复制这一句话，并且去掉下面的反斜杠，谢谢！
\`print('hello nick')\`
```

效果：

`print('hello nick')`

2.10 插入链接（链接显示）
---------------

代码：

```
<https://www.52pojie.cn/?1633628>
```

效果：

[https://www.52pojie.cn/?1633628](https://www.52pojie.cn/?1633628)

2.11 插入链接（链接描述显示）
-----------------

代码：

```
[nickchen博客](https://www.cnblogs.com/nickchen121/p/10718112.html "nickchen博客")
```

效果：

[nickchen 博客](https://www.cnblogs.com/nickchen121/p/10718112.html)

2.12 插入图片（链接）
-------------

代码：

```
![数据类型总结-搞笑结束.jpg?x-oss-process=style/watermark](http://www.chenyoude.com/Python从入门到放弃/数据类型总结-搞笑结束.jpg?x-oss-process=style/watermark '描述信息')
```

效果：

[![](https://img-blog.csdnimg.cn/img_convert/3dfe151122150672ec4ca6e36e5c15b0.png)](http://www.chenyoude.com/Python从入门到放弃/数据类型总结-搞笑结束.jpg?x-oss-process=style/watermark)

2.13 插入图片（图片路径）
---------------

*   绝对路径：.md 文本同目录下图片的名字，如`数据类型总结-搞笑结束.jpg?x-oss-process=style/watermark`
*   相对路径：图片在电脑中的路径地址，如`\Users\nick\mac\desktop\数据类型总结-搞笑结束.jpg?x-oss-process=style/watermark`

代码：

```
![数据类型总结-搞笑结束.jpg?x-oss-process=style/watermark](数据类型总结-搞笑结束.jpg?x-oss-process=style/watermark '描述信息')

![数据类型总结-搞笑结束.jpg?x-oss-process=style/watermark](\Users\nick\mac\desktop\数据类型总结-搞笑结束.jpg?x-oss-process=style/watermark '描述信息')
```

2.14 有序列表
---------

代码：

```
1. one
2. two 
3. three
```

效果：

1.  one
2.  two
3.  three

2.15 无序列表
---------

代码：

```
* one
* two
* three
```

效果：

*   one
*   two
*   three

2.16 分割线
--------

代码：

```
---
```

效果：

* * *

2.17 表格而且第二行必须得有，并且第二行的冒号代表对齐格式，分别为居中；右对齐；左对齐）：
-----------------------------------------------

```
name | age | sex 
:-:|:-|-:
tony|20|男
lucy|18|女
```

效果：

<table><thead><tr><th>name</th><th>age</th><th>sex</th></tr></thead><tbody><tr><td>nick</td><td>19</td><td>男</td></tr><tr><td>jason</td><td>18</td><td>女</td></tr></tbody></table>

2.18 数学公式（行内嵌）
--------------

代码：

```
内嵌数学公式$\sum_{i=1}^{10}f(i)\,\,\text{thanks}$
```

效果：

内嵌数学公式 $\sum_{i=1}^{10}f(i)\,\,\text{thanks}$

2.19 数学公式（块状）
-------------

代码：

```
$$
\sum_{i=1}^{10}f(i)\,\,\text{thanks}
$$
```

效果：

\sum_{i=1}^{10}f(i)\,\,\text{thanks}

三、总结
====

以上所述就是 Markdown 的基本标签，虽然不多，但是可以解决大部分情况。

> 可以用 20% 的标签完成 80% 的效果

基础 markdown 语法参考：[https://www.appinn.com/markdown/](https://www.appinn.com/markdown/)

插入数学公式 markdown 语法参考：[https://www.zybuluo.com/codeep/note/163962](https://www.zybuluo.com/codeep/note/163962)

  
这位道友，你学废了吗？![](https://www.52pojie.cn/uc_server/images/noavatar_middle.gif)codyy

> [luanshils 发表于 2021-5-30 09:16](https://www.52pojie.cn/forum.php?mod=redirect&goto=findpost&pid=38738524&ptid=1449716)  
> 难怪每个 github 项目都有 md 文件

md 好是好，但个人认为网络上使用会更方便，对于本地使用还是麻烦了一些，主要像图片的使用，它不像 word 之类，你粘贴了一张图片进去，图片是保存在这个文档内了，而只是记录了一个图片路径或者是图片网址，所以你用 md 写了个包含图片的文档发给同事，结果对方并不能看到图片。所以一些 md 软件会支持贴图的时候自动上传到图床，但总归是比较麻烦的，万一你是在没有网络的环境下写文档呢，或者常用的图床访问故障，你只能把图片和 md 文档放在同一个文件夹里，一起给同事了。。。![](https://avatar.52pojie.cn/data/avatar/000/23/25/18_avatar_middle.jpg)不懂破解 _ 本帖最后由 不懂破解 于 2021-5-30 10:25 编辑_  

> [luanshils 发表于 2021-5-29 23:52](https://www.52pojie.cn/forum.php?mod=redirect&goto=findpost&pid=38737212&ptid=1449716)  
> 想知道 md 有啥好处

> Markdown 可以看成是简化版的 HTML 标记语言，吾爱论坛的发帖回帖就可以用它来写。还可以用于写博客、写作、做笔记，代替商业的 OneNote、印象笔记、麦库记事、有道云笔记、为知笔记。

### 比较好的 Markdown 软件：

*   [Joplin](https://joplinapp.org/)（Win & macOS & Linux 等全平台支持，包括手机端）
*   [Obsidian](https://obsidian.md/)（Win & macOS & Linux 全平台支持，手机端内测中）
*   [Typora](https://www.typora.io/)（Win & macOS & Linux 全平台支持，无手机端）
*   [VS Code](https://code.visualstudio.com/)（Win & macOS & Linux 全平台支持，通过安装插件 MPE 支持）

### Markdown 软件界面截图：

![](https://attach.52pojie.cn/forum/202105/30/101718htr0ttpy0ytzqhyt.jpg)

**Joplin.jpg** _(114.13 KB, 下载次数: 0)_

[下载附件](forum.php?mod=attachment&aid=MjI5ODExNnxlNGRlNmYwOXwxNjIyNDM4NjMwfDB8MTQ0OTcxNg%3D%3D&nothumb=yes)

Joplin

2021-5-30 10:17 上传

![](https://attach.52pojie.cn/forum/202105/30/102024q2p525q7tl7bb777.jpg)

**Obsidian.jpg** _(475.89 KB, 下载次数: 0)_

[下载附件](forum.php?mod=attachment&aid=MjI5ODExOXwwNzAxNDFmOXwxNjIyNDM4NjMwfDB8MTQ0OTcxNg%3D%3D&nothumb=yes)

Obsidian

2021-5-30 10:20 上传

  
![](https://attach.52pojie.cn/forum/202105/30/101919r1p6mrpfsmwuaotz.png)

**Typora.png** _(240.12 KB, 下载次数: 0)_

[下载附件](forum.php?mod=attachment&aid=MjI5ODExOHw1ZmMzNDI0MnwxNjIyNDM4NjMwfDB8MTQ0OTcxNg%3D%3D&nothumb=yes)

Typora

2021-5-30 10:19 上传

  
![](https://attach.52pojie.cn/forum/202105/30/101723w9ot9mnssnyykzbi.png)

**VSCode.png** _(123.71 KB, 下载次数: 0)_

[下载附件](forum.php?mod=attachment&aid=MjI5ODExN3xlYjYzMDE0NXwxNjIyNDM4NjMwfDB8MTQ0OTcxNg%3D%3D&nothumb=yes)

VS Code

2021-5-30 10:17 上传![](https://avatar.52pojie.cn/data/avatar/000/23/67/85_avatar_middle.jpg)luanshils 想知道 md 有啥好处 ![](https://www.52pojie.cn/uc_server/images/noavatar_middle.gif) deTrident markdown 可博客更搭配 ![](https://www.52pojie.cn/uc_server/images/noavatar_middle.gif) AsuraSong 谢谢楼主分享，值得学习 ![](https://www.52pojie.cn/uc_server/images/noavatar_middle.gif) lingyinGR 好东西。收藏了 ![](https://avatar.52pojie.cn/data/avatar/000/55/20/41_avatar_middle.jpg) pwp # 现场测试一下  
## 收藏了。![](https://www.52pojie.cn/uc_server/images/noavatar_middle.gif)smallmin

> [luanshils 发表于 2021-5-29 23:52](https://www.52pojie.cn/forum.php?mod=redirect&goto=findpost&pid=38737212&ptid=1449716)  
> 想知道 md 有啥好处

调格式方便、简洁，不用专门花心思美化。且不同系统不同软件同一份 md 文件展示的效果一样（Word 可做不到哦）![](https://www.52pojie.cn/uc_server/images/noavatar_middle.gif)digman 谢谢!  
markdown 的标准语法, ### and ###, and 前后要不要空格?![](https://www.52pojie.cn/uc_server/images/noavatar_middle.gif)tbloy 学习一下新东西，不错。![](https://avatar.52pojie.cn/data/avatar/000/71/01/54_avatar_middle.jpg)GenW

###### MD 后文本显示很漂亮，目前 outlook 也在搭配着，不过那个插件不是很完善。