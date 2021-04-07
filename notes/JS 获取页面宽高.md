> 本文由 [简悦 SimpRead](http://ksria.com/simpread/) 转码， 原文地址 [www.cnblogs.com](https://www.cnblogs.com/dreamperson/p/9367346.html)

网页可见区域宽： document.body.clientWidth   
网页可见区域高： document.body.clientHeight   
网页可见区域宽： document.body.offsetWidth (包括边线的宽)   
网页可见区域高： document.body.offsetHeight (包括边线的高)   
网页正文全文宽： document.body.scrollWidth   
网页正文全文高： document.body.scrollHeight   
网页被卷去的高： document.body.scrollTop   
网页被卷去的左： document.body.scrollLeft   
网页正文部分上： window.screenTop   
网页正文部分左： window.screenLeft   
屏幕分辨率的高： window.screen.height   
屏幕分辨率的宽： window.screen.width   
屏幕可用工作区高度： window.screen.availHeight   
屏幕可用工作区宽度： window.screen.availWidth 

**在我本地测试当中：**   
在 IE、FireFox、Opera 下都可以使用   
document.body.clientWidth   
document.body.clientHeight   
即可获得，很简单，很方便。   
而在公司项目当中：   
Opera 仍然使用   
document.body.clientWidth   
document.body.clientHeight   
可是 IE 和 FireFox 则使用   
document.documentElement.clientWidth   
document.documentElement.clientHeight   
原来是 W3C 的标准在作怪啊   
<!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Transitional//EN" "http://www.w3.org/TR/xhtml1/DTD/xhtml1-transitional.dtd"> 

**如果在页面中添加这行标记的话**   
**在 IE 中**：   
document.body.clientWidth ==> BODY 对象宽度   
document.body.clientHeight ==> BODY 对象高度   
document.documentElement.clientWidth ==> 可见区域宽度   
document.documentElement.clientHeight ==> 可见区域高度   
**在 FireFox 中**：   
document.body.clientWidth ==> BODY 对象宽度   
document.body.clientHeight ==> BODY 对象高度   
document.documentElement.clientWidth ==> 可见区域宽度   
document.documentElement.clientHeight ==> 可见区域高度   
?

**在 Opera 中**：   
document.body.clientWidth ==> 可见区域宽度   
document.body.clientHeight ==> 可见区域高度   
document.documentElement.clientWidth ==> 页面对象宽度（即 BODY 对象宽度加上 Margin 宽）   
document.documentElement.clientHeight ==> 页面对象高度（即 BODY 对象高度加上 Margin 高）   
而如果没有定义 W3C 的标准，则   
**IE 为**：   
document.documentElement.clientWidth ==> 0   
document.documentElement.clientHeight ==> 0   
**FireFox 为**：   
document.documentElement.clientWidth ==> 页面对象宽度（即 BODY 对象宽度加上 Margin 宽）document.documentElement.clientHeight ==> 页面对象高度（即 BODY 对象高度加上 Margin 高）   
**Opera 为**：   
document.documentElement.clientWidth ==> 页面对象宽度（即 BODY 对象宽度加上 Margin 宽）document.documentElement.clientHeight ==> 页面对象高度（即 BODY 对象高度加上 Margin 高）

真是一件麻烦事情，其实就开发来看，宁可少一些对象和方法，不使用最新的标准要方便许多啊。   
有时候需要取页面的底部, 就会用到 document.body.clientHeight , 在 HTML 标准中 (这一句就能取到整个页面的高度, 不论 body 的实际内容到底有多高, 例如, 1074*768 的分辨率, 页面最大化时, 这个高度约为 720 , 即使页面上只有一句”hello world” , 也仍然取到 720.   
可是在 XHTML 中, 如果 body 体中只有一行, 则 document.body.clientHeight 只能取到那一行的高度, 约 20px, 这时如何还想取到整个页面的高度, 就要用 document.documentElement.clientHeight 来获取了.   
原因是: 在 HTML 中, body 是整个 DOM 的根, 而在 XHTML 中, document 才是根, body 不再是根, 所以取 body 的属性时, 不能再取到整个页面的值. 

**区别新旧标准的行是**:   
<!DOCTYPE HTML PUBLIC “-//W3C//DTD HTML 4.0 Transitional//EN” >   
<!DOCTYPE html PUBLIC “-//W3C//DTD XHTML 1.0 Transitional//EN” “http://www.w3.org/TR/xhtml1/DTD/xhtml1-transitional.dtd“>