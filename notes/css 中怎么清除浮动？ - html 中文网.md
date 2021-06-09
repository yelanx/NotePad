> 本文由 [简悦 SimpRead](http://ksria.com/simpread/) 转码， 原文地址 [www.html.cn](https://www.html.cn/qa/css3/13783.html)

> css 中清除浮动的方法：1、使用 clear:both 清除浮动；2、利用伪元素 clearfix 来清除浮动；3、使用 CSS 的 overflow 属性。

在我们写代码的时候，有时因为使用了 float 浮动元素而导致页面中某些元素不能正确的显示，接下来在文章中将为大家详细介绍几种清除浮动的方法，具有一定的参考价值，希望对大家有所帮助。

![](https://img.html.cn/upload/article/000/000/006/5dc538515be29795.jpg)

**浮动导致的后果：**

（1）由于浮动元素脱离了文档流，所以父元素的高度无法被撑开，影响了与父元素同级的元素  
（2）与浮动元素同级的非浮动元素会跟随其后，因为浮动元素脱离文档流不占据原来的位置  
（3）如果该浮动元素不是第一个浮动元素，则该元素之前的元素也需要浮动，否则容易影响页面的结构显示

例：在一个 div 中设置三个 div，让三个 div 的来撑开父元素

```
<style>
    .box{border:1px solid #ccc;background:pink;}
    .red{width:100px;height:100px;background: red;}
    .green{width:100px;height:100px;background:green;}
    .blue{width:100px;height:100px;background: blue;}
</style>
<body>
<div class="box">
    <div class="red"></div>
    <div class="green"></div>
    <div class="blue"></div>
</div>
```

效果图：

![](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAADIAAAAyCAYAAAAeP4ixAAACbklEQVRoQ+2aMU4dMRCGZw6RC1CSSyQdLZJtKQ2REgoiRIpQkCYClCYpkgIESQFIpIlkW+IIcIC0gUNwiEFGz+hlmbG9b1nesvGW++zxfP7H4/H6IYzkwZFwQAUZmpJVkSeniFJKA8ASIi7MyfkrRPxjrT1JjZ8MLaXUDiJuzwngn2GJaNd7vyP5IoIYY94Q0fEQIKIPRGS8947zSQTRWh8CwLuBgZx479+2BTkHgBdDAgGAC+fcywoyIFWqInWN9BSONbTmFVp/AeA5o+rjKRJ2XwBYRsRXM4ZXgAg2LAPzOCDTJYQx5pSIVlrC3EI45y611osMTHuQUPUiYpiVooerg7TWRwDAlhSM0TuI+BsD0x4kGCuFSRVzSqkfiLiWmY17EALMbCAlMCmI6IwxZo+INgQYEYKBuW5da00PKikjhNNiiPGm01rrbwDwofGehQjjNcv1SZgddALhlJEgwgJFxDNr7acmjFLqCyJuTd6LEGFttpmkYC91Hrk3s1GZFERMmUT01Xv/sQljjPlMRMsxO6WULwnb2D8FEs4j680wScjO5f3vzrlNJszESWq2LYXJgTzjZm56MCHf3zVBxH1r7ftU1splxxKYHEgoUUpTo+grEf303rPH5hxENJqDKQEJtko2q9zGeeycWy3JhpKhWT8+NM/sufIhBwKI+Mta+7pkfxKMtd8Qtdbcx4dUQZcFCQ2I6DcAnLUpf6YMPxhIDDOuxC4C6djoQUE6+tKpewWZ1wlRkq0qUhXptKTlzv93aI3jWmE0Fz2TeujpX73F9TaKy9CeMk8vZusfBnqZ1g5GqyIdJq+XrqNR5AahKr9CCcxGSwAAAABJRU5ErkJggg==)

加了 float:left 之后，父元素无法被撑开

![](https://img.html.cn/upload/article/000/000/006/5dc53780ba39b695.jpg)

**清除浮动的方法**

**（1）使用 clear:both 清除浮动**  

在代码中在放一个空的 div 标签，然后给这个标签设置 clear:both 来清除浮动对页面的影响。它的优点是简单，方便兼容性好，但是一般情况下不建议使用该方法，因为会造成结构混乱，不利于后期维护

```
<div style="clear: both"></div>
```

**（2）利用伪元素 clearfix 来清除浮动**

给父级元素添加了一个: after 伪元素，通过清除伪元素的浮动，达到撑起父元素高度的目的

```
.clearfix:after{
    content:"";
    display:block;
    visibility:hidden;
    clear:both;
    }
```

**（3）使用 CSS 的 overflow 属性**

当给父元素设置了 overflow 样式，不管是 overflow:hidden 或 overflow:auto 都可以清除浮动只要它的值不为 visible 就可以了，它的本质就是建构了一个 BFC，这样使得达到撑起父元素高度的效果

```
.box{border:1px solid #ccc;background:#eff2f4;overflow: auto}
```

更多[前端开发](https://www.html.cn/)知识，请查阅 HTML 中文网 ！！

以上就是 css 中怎么清除浮动？的详细内容，更多请关注 html 中文网其它相关文章！