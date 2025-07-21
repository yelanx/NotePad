> 本文由 [简悦 SimpRead](http://ksria.com/simpread/) 转码， 原文地址 [developer.chrome.com](https://developer.chrome.com/blog/if-article?hl=zh-cn)

> 了解新的 CSS if 函数，该函数可为样式查询和媒体查询等动态样式提供更简洁的开发者界面。

![](https://developer.chrome.com/_static/images/translated.svg?hl=zh-cn) 此页面由 [Cloud Translation API](//cloud.google.com/translate/?hl=zh-cn) 翻译。 

*   [Chrome for Developers](https://developer.chrome.com/?hl=zh-cn)
*   [Blog](https://developer.chrome.com/blog?hl=zh-cn)

该内容对您有帮助吗？

使用新的 if() 函数的 CSS 条件语句 bookmark_borderbookmark 使用集合让一切井井有条 根据您的偏好保存内容并对其进行分类。
=============================================================================

*   本页内容
*   [演示：内嵌媒体查询](#demo_inline_media_queries)
*   [演示：内嵌支持查询](#demo_inline_support_queries)
*   [演示：可视化界面状态](#demo_visualizing_ui_state)
*   [后续步骤](#whats_next)

.wd-authors { --avatar-size: 65px; display: flex; gap: 2em; } .wd-author { display: flex; flex-wrap: wrap; gap: 1em; line-height: calc(var(--avatar-size) / 2); } .wd-author img { border-radius: 50%; height: var(--avatar-size, 65px); width: var(--avatar-size, 65px); } .dcc-authors { --avatar-size: 65px; display: flex; gap: 2em; } .dcc-author { display: flex; flex-wrap: wrap; gap: 1em; line-height: calc(var(--avatar-size) / 2); } .dcc-author img { border-radius: 50%; height: var(--avatar-size, 65px); width: var(--avatar-size, 65px); } .dcc-author__links { display: flex; } .dcc-author__links a { margin-inline-end: 6px; } .dcc-author__links a:last-of-type { margin-inline-end: 0; }

![](https://web.dev/images/authors/unakravets.jpg?hl=zh-cn) Una Kravets [ X ](https://twitter.com/una) [ Mastodon ](https://front-end.social/@una) [首页](https://una.im) 

发布时间：2025 年 7 月 1 日

从 Chrome 137 开始，您可以使用 `if()` 函数试用 CSS 内嵌条件语句。`if()` 为样式查询和媒体查询等动态样式提供了更简洁的开发者界面，但存在一些关键差异，您可以在本文中了解这些差异。

CSS `if()` 函数的运作方式是使用一系列条件 - 值对，其结构如下所示：

```
property: if(condition-1: value-1; condition-2: value-2; condition-3: value-3);
```

您可以提供 `else` 语句，如果没有其他条件满足，系统会使用该语句：

```
property: if(condition-1: value-1; condition-2: value-2; condition-3: value-3; else: value-4);
```

目前，`if()` 支持三种不同类型的查询：

*   `style()`：样式查询
*   `media()`：媒体查询
*   `supports()`：支持查询

**注意**： 样式查询与在 `if()` 函数中使用 `style()` 之间的主要区别在于，`if()` 会立即应用于样式设置的元素。由于它不依赖于父元素来查询值，因此您可以立即查询样式值。

下面我们通过一些示例来了解一下：

演示：内嵌媒体查询
---------

使用 `if()` 是将内嵌媒体查询添加到样式的绝佳方式。例如，您可以检查用户的主题设置（浅色或深色），或针对视口宽度执行内嵌媒体查询。以下示例展示了指针设备的媒体查询。对于具有精细指针（例如鼠标）的设备，按钮的默认大小为 30 像素；但对于具有粗略指针的触摸屏设备，按钮的大小应至少为 44 像素，以便提供适当的触摸间距，从而更易于访问。

```
button {
  aspect-ratio: 1;
  width: if(media(any-pointer: fine): 30px; else: 44px);
}
```

上述代码会与以下媒体查询产生相同的结果：

```
button {
  aspect-ratio: 1;
}

@media (any-pointer: fine) {
  button {
    width: if(media(any-pointer: fine): 30px; else: 44px);
  }
}
```

使用 `if()` 格式后，您可以内嵌逻辑，而无需在两个不同的位置使用样式设置逻辑。

查看 <a href="https://codepen.io">CodePen</a> 上 web.dev (<a href="https://codepen.io/web-dot-dev">@web-dot-dev</a>) 的 <a href="https://codepen.io/web-dot-dev/pen/xbGBEaY"> if() 函数媒体查询 </a > 笔记。 演示：使用 `if()` 可在具有课程指针的设备上增大按钮的字体大小。

演示：内嵌支持查询
---------

使用 `if()` 的另一种方法是创建内嵌支持查询。例如，如需检查是否支持 OKLCH 等广色域色彩，您可以使用以下方法：

```
body {
  background-color: if(
    supports(color: oklch(0.7 0.185 232)): oklch(0.7 0.185 232);
    else: #00adf3;
  );
  
  &::after {
    content: if(
    supports(color: oklch(0.7 0.185 232)): "Your browser supports OKLCH";
    else: "Your browser does not support OKLCH";
    );
  }
}
```

使用此代码时，如果浏览器支持 `oklch` 色彩空间，用户会看到更鲜艳的颜色，并且还会在 `::after` 伪内容中收到消息：“您的浏览器支持 OKLCH”。

请参阅 <a href="https://codepen.io">CodePen</a> 上 web.dev (<a href="https://codepen.io/web-dot-dev">@web-dot-dev</a>) 的 <a href="https://codepen.io/web-dot-dev/pen/PwqLGXM"> if() 是否支持查询 </a > 这篇笔记。 使用 `if()` 函数的支持查询。

如需了解详情并了解 RGB 和更高级的色彩空间之间的区别，请访问 [oklch.com](https://oklch.com/#0.7,0.185,232,100) 查看颜色选择器和资源。

**注意**： 若要使用此类支持查询，首先需要支持 `if()`，因此此方法适用于在 `if()` 之后发布的面向未来的功能。

演示：可视化界面状态
----------

您还可以使用 `if()` 实现基于状态的样式设置。例如，根据进度条的界面状态（待处理或已完成）设置其样式。直接将状态存储在数据属性或自定义属性中，然后使用 `if()` 对该属性内嵌应用样式。

```
<div class="card" data-status="complete">
  ...
</div>
```

```
.card {
  --status: attr(data-status type(<custom-ident>));
  border-color: if(
                style(--status: pending): royalblue;
                style(--status: complete): seagreen;
                else: gray);
  background-color: if(
                style(--status: pending): #eff7fa;
                style(--status: complete): #f6fff6;
                else: #f7f7f7);
}
```

请参阅 <a href="https://codepen.io">CodePen</a> 上 web.dev (<a href="https://codepen.io/web-dot-dev">@web-dot-dev</a>) 的 <a href="https://codepen.io/web-dot-dev/pen/KwpEgJg"> 使用 if() 的状态标签 </a>。 使用 `if()` 函数为属性内嵌的状态标签设置样式。

在 `if()` 函数内使用 `style()` 时，您可以直接为所定位的元素设置样式，而无需 CSS 样式查询所需的父元素。

此演示展示了如何使用 `if()` 支持 CSS 的新架构方法的基本示例。与使用类相比，使用 CSS 自定义属性的一个优势是，您可以轻松地在 CSS 中更新它们。例如，可以使用媒体查询或 `:hover` 等伪状态进行更新。

后续步骤
----

添加 `if()` 为 CSS 开发者提供了新的架构机会。随着样式查询等技术的不断发展，我们有望在 `if()` 函数中实现范围查询，并且这些查询在与即将推出的自定义函数提案 (CSS `@function`) 结合使用时也非常有用。

如需详细了解这些功能，请参阅以下资源：

*   [演示 3 的 3 分钟视频演示](https://www.youtube.com/watch?v=Apn8ucs7AL0&hl=zh-cn)
*   [Lea Verou 撰写的文章](https://lea.verou.me/blog/2024/css-conditionals/)

该内容对您有帮助吗？