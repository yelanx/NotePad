> 本文由 [简悦 SimpRead](http://ksria.com/simpread/) 转码， 原文地址 [www.v2ex.com](https://www.v2ex.com/t/962276)

上次帖子: [AI 生成前端组件的价值思考🤔️](https://www.v2ex.com/t/960489)

目标
--

1.  简单快速：比如你要亲自告诉 AI ，登录按钮设置成圆角，蓝色，那一点都不快速，有这时间样式代码都写好了。
2.  可控：比如你直接跟 AI 说，请生成一个登录框，要好看大气，你提供的信息只有这么多，那 AI 生成出来的变数自然会很大，如果你是对结果有要求，那肯定不符合你的预期。

整体设计思路
------

1.  提供信息：比如组件名，描述，还有组件内有哪些元素，提供的信息完整了，AI 才能生成我们想要的组件。
2.  预览结构：我叫它 “渐进式” 生成，即先让 AI 描绘一下即将生成的组件结构，然后我们在这基础上微调结构，再生成。要求一是格式简单，可读性强，修改起来容易；二是方便 AI 理解，不要引入过多新概念名词，综合考虑后发现 markdown 多级列表格式比较合适。
3.  生成结果：组装提示词，让 AI 生成组件。

如何变得更简单快速
---------

这是目标之一，我们让 AI 生成组件，是为了提高效率，但如果要提供的描述很多很精确，又很费时间。

所以这里采取的一种解决办法就是让 AI 生成一些选项，我们再修改，这样就快多了，将题目从 “思考题” 变成了“选择题”😊。

如何查看生成的组件
---------

生成代码后，如果要复制到自己项目里，改改代码才能用，那操作上就比较繁琐，所以想着自己在网页上集成个前端沙盒，可以查看生成的组件结果。

另外再提供个模板项目，可以方便查看生成的组件。

实践碰到的一些问题
---------

示例值问题，比如用户资料组件，要展示头像，名词，简介等信息，这些示例值可以手输也可以让 AI 生成，方便查看实际的效果。

图片问题，如果我们不限制，AI 可能会生成一些不存在的图片链接，导致图片无法显示，所以我们可以让 AI 使用固定的图片地址来替代。

图标问题，AI 可以生成 svg 内容，但那样太大了，会占用太多 token ，费用更高，而且容易导致生成因 token 限制而中断，所以我们可以让 AI 使用固定的图标组件来代替。此外，openai 的训练数据里也包括了热门的图标库，所以我们也可以让 AI 自己选取热门图标库里的图标来代替生成 svg 图标。

生成的组件不符合要求怎么办
-------------

这个要多方面考虑

1.  如果是一些细节上的问题，可以直接改生成的代码
2.  如果有异常无法运行，可以考虑重新生成，或者如果问题比较小就直接改代码
3.  如果生成的布局结构不符合预期，可以调整第二步预览步骤里的结构描述
4.  如果问题过大，可以考虑调整描述或不使用 AI 生成

AI 适合生成哪些组件
-----------

1.  ❌基础组件，如输入框，按钮，下拉框等，这种一般使用 UI 库，而且 AI 也无法生成复杂的动效等。
2.  ✅高阶组件，这类组件一般由若干内容组装而成，像搭乐高积木一样，如登录表单，用户资料卡片，文章卡片等，这种是最适合的。
3.  ❌整个页面，如果你让 AI 直接生成整个页面，那 AI 会将页面内每个组件都给你生成了，这样不仅 token 消耗大，容易达到上限，而且组件的封装不好，每个组件可控性也会降低，所以还是让 AI 生成组件后，再自行拼凑在一起吧。

实践展示
----

### 提供信息

1.  输入组件名："a blog card"
2.  让 AI 推荐业务对象字段，然后删掉不需要的
3.  让 AI 生成示例值，不用改什么
4.  让 AI 推荐组件内的元素（指按钮，链接之类的，业务对象外的），然后删掉不需要的

![](https://i.v2ex.co/1l997JM9.png)

### 预览结构

1.  让 AI 生成预览结构，然后看看结构是否符合预期

![](https://i.v2ex.co/WR8L8Z8o.png)

### 生成结果

1.  可以看到，AI 按照预览结构来生成组件
2.  组件内容展示了我们提供的示例值
3.  我指定了图标库，AI 自动选取了图标

![](https://i.v2ex.co/w704P2w6.png)

#### 生成的代码如下：

为了方便使用，我将业务对象接口定义，示例值都塞组件代码里了。

```
import React from 'react';
import { FiShare2, FiHeart } from 'react-icons/fi';

interface BlogCardProps {
  data: BlogCard;
}

export interface BlogCard {
  title: string;
  author: string;
  date: string;
  image: string;
  description: string;
  tags: string[];
}

export const Example: BlogCard = {
  "title": "📚 My Journey into Programming",
  "author": "👩‍💻 Jane Doe",
  "date": "📅 September 15, 2022",
  "image": "/avatar.png",
  "description": "📝 In this blog post, I will share my personal journey into programming and how it has changed my life. From my first line of code to becoming a professional developer, I will discuss the challenges, triumphs, and lessons learned along the way.",
  "tags": ["✨ Programming", "🌟 Career", "💡 Personal Development"]
}

const BlogCardComp: React.FC<BlogCardProps> = ({ data }) => {
  return (
    <article class>
      <div class>
        <img src={data.image} alt={data.title} class />
      </div>
      <div class>
        <h3 class>{data.title}</h3>
        <div class>
          <span class>{data.author}</span>
          <span class>|</span>
          <span class>{data.date}</span>
        </div>
        <p class>{data.description}</p>
        <div class>
          {data.tags.map((tag, index) => (
            <span key={index} class>
              {tag}
            </span>
          ))}
        </div>
      </div>
      <div class>
        <a href="#" class>
          Read More
        </a>
        <button class>
          <FiShare2 class />
        </button>
        <button class>
          <FiHeart class />
        </button>
      </div>
    </article>
  );
};

export default BlogCardComp;
```

### 其他例子

![](https://i.v2ex.co/8bHx05vw.png)

[AI 生成前端组件发布了🎉欢迎试用提意见](https://www.v2ex.com/t/963005)