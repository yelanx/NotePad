> 本文由 [简悦 SimpRead](http://ksria.com/simpread/) 转码， 原文地址 [www.v2ex.com](https://www.v2ex.com/t/960489)

想法来源
----

这个想法来源于我自己的需求，我自己首先就是最精准的目标用户，在这个 AI 时代，我希望 AI 可以帮我尽量多地干活。 结合自己的日常独立开发情况，发现花在调前端组件样式上的时间很多，因此思考能不能让 AI 把这部分活接过去，或者能提高一些效率也行。

适合用户
----

外行或刚入门的前端开发者不是此 AI 的目标用户，此 AI 目标用户是已入门的前端开发者，并且使用 tailwind 作为 UI 库。

实际场景
----

### 非目标场景：figma 画原型图，然后前端照着做

这种情况下，UI 设计师先干活，做出原型图，然后前端照着写组件，前端需要的组件布局，颜色，间距等都是确定的值，用 AI 生成组件并不能提高效率，反而会降低效率。

### 目标场景：前端直接写组件，然后调样式

这种情况下，我们是怎么写组件的呢？ 一种方式是凭经验直接从零手撸，然后反复调整样式看效果，很多时候我们都这么做； 如果脑子里没有概念，可能会去网上找现成的网站进行参考，对照着做； 此外我们还可能直接网上找现成的模板，然后复制下来再改改。

此 AI 就是为了提高这种场景下的开发效率而专门设计的。

提效的点
----

搞清楚目标场景下，时间都花在哪里，才能进行提效。

按个人实践经验，如果从零撸组件，时间通常大量花在布局，调样式上； 如果网上找现成组件对照着做，会快一些，因为大脑不用太多思考，所以会更轻松，从创造的活更多的变成体力活了，而干体力活也是要花时间的； 如果网上找现成的组件模板改，时间一是花在找模板与比较哪个更好，二是改模板花的时间，包括先理解下载的代码，然后调整，这种模式最大问题是大部分时候，网上下载的现成固定模板跟我们的实际需求并不匹配，比如布局会有些不同，多了些元素，少了些字段，样式要改等，如果模板过于复杂，可能最终改的面目全非，或改出一些隐藏毛病，回过头看还不如从零手撸。

搞清楚了时间都花在哪里，下面就可以进行针对性提效了：

功能介绍
----

此 AI 通过简单的描述来生成组件：组件名称，字段描述。

生成的组件特点：

*   支持深色模式
*   支持响应式，可能需要额外的描述
*   属性提取，比如将标题提取为 props 变量，可以在调用时传入
*   支持调色板，比如你的应用有固定的一套颜色

粒度思考
----

开始可以是粗粒度的，比如就提供个组件名称叫 “登录” 组件，然后 AI 随机生成个登录组件，可能有名字，密码输入框，与登录按钮。

然后你可能对布局不满意，比如登录按钮位置不对；或发现少了元素，比如没有注册按钮。 那就可以添加更多描述，然后 AI 重新生成更精确的，反复如此直到满意为止。

但是不要太细，比如写 “设置标题大小为 32px”，或 “设置按钮颜色为 #FF0000”，那还不如生成后你复制代码，然后手动改来的快。

原理思考
----

AI 为什么能节省我们的开发时间？ 因为我们提供了组件名称，与需要的字段列表，AI 能像人一样思考，能从语义上理解你想做的东西，它就知道对于这个组件，这些字段应该怎样布局，字体大小，颜色，样式，间距要怎么设置，因此这部分思考时间 AI 就帮你节省了。

你是否需要它？
-------

你对组件的样式要求越低，它就越适合你； 反过来你对组件的样式要求越高，比如指定的布局，大小，颜色，间距，你明确的要求越多，AI 能帮你思考节省的地方就越少，它就越不适合你。

实践
--

### 初次尝试

提供的描述：

```
名称：a profile card
描述：a small user profile card to display user information
元素列表：
- profile picture
- username
- bio
- link to see detail
```

生成的代码：

```
import React from 'react'  
  
interface ProfileCardProps {  
  profilePicture: string  
  username: string  
  bio: string  
  link: string  
}  
  
const ProfileCard: React.FC<ProfileCardProps> = ({  
  profilePicture,  
  username,  
  bio,  
  link,  
}) => {  
  return (  
    <div class>  
      <div class>  
        <img  
          src={profilePicture}  
          alt="Profile Picture"  
          class  
        />  
        <div class>  
          <h2 class>{username}</h2>  
          <p class>{bio}</p>  
        </div>  
      </div>  
      <a  
        href={link}  
        class  
      >  
        See Detail  
      </a>  
    </div>  
  )  
}  
  
export default ProfileCard
```

生成的效果 (设置了字段值)：

![](https://i.v2ex.co/NC4wHy7v.png)

思考： 可以看到，描述里我们只提供了元素的名称，并没有提供位置，大小，颜色等信息，AI 帮我们思考出了这些元素的布局与样式。

### 添加字段

假如我们需要加用户角色与登出按钮。

提供的描述：

```
名称：a profile card
描述：a small user profile card to display user information
元素列表：
- profile picture
- username
- bio
- link to see detail
- badge style user role
- button to logout
```

生成的效果：

![](https://i.v2ex.co/7pmweMTQ.png)

思考： 可以看到，提供了两个更多字段后，AI 生成了另一种合理的布局，如果多生成几次，每次的效果可能都不太一样，但基本都是合理且好看的。 另一个就是我们描述的`badge style`并没有实现，说明 AI 具有理解与思考能力，并不是完全死板地按我们的指令做。这种情况下如果一定要 badge 样式，直接在生成的代码上改可能比较快，而不是提供非常详细的描述，一定要让 AI 生成出你想要的效果。

### 微调布局

假如我们对布局有一些要求，比如我想用户 role 以 badge 样式放右上角。

提供的描述：

```
名称：a profile card
描述：a small user profile card to display user information
元素列表：
- top:
	- profile picture in left
	- username and bio in right
- bottom:
	- link to see detail
	- button to logout
- badge style user role in top right with absolute position
```

生成的效果：

![](https://i.v2ex.co/YR836ngn.png)

思考： 实际微调下来，小问题还是有一些的，生成的并不很稳定，可能在怎么描述上要花点功夫。

### 其他例子

![](https://i.v2ex.co/Wu39zC79.png)

[AI 生成前端组件的动手尝试💪](https://www.v2ex.com/t/962276)

[AI 生成前端组件发布了🎉欢迎试用提意见](https://www.v2ex.com/t/963005)