> 本文由 [简悦 SimpRead](http://ksria.com/simpread/) 转码， 原文地址 [mp.weixin.qq.com](https://mp.weixin.qq.com/s/u7RDsgQzh19UkH02AotPfQ)

《剑指 Offer：专项突破版》是一个算法题集。该题单包含了程序员在准备面试过程中必备的数据结构与算法知识。具体包含：

*   数据结构：整数、数组、字符串、链表、栈、栈、队列、树、堆和前缀树。
    
*   算法：二分查找、排序、回溯法、动态规划和图搜索。
    

本文来分享下栈部分题的解法~

栈介绍
---

> 栈是操作受限的数组。栈的插入和删除操作都发生在数组的末尾 (也叫栈顶)。它的特点是 “后入先出”：后入栈的元素先出来。

基于 js 的数组，很容易实现栈：

```
class Stack {  constructor() {    this.data = [];  }  // 入栈  push(val) {    this.data.push(val);  }  // 出栈  pop() {    return this.data.pop();  }  // 查询栈顶元素  peek() {    return this.data.length ? this.data[this.data.length - 1] : null;  }}
```

题 1 - 剑指 Offer II 036. 后缀表达式
----------------------------

> 题目：后缀表达式是一种算术表达式，它的操作符在操作数的后面。输入一个用字符串数组表示的后缀表达式，请输出该后缀表达式的计算结果。假设输入的一定是有效的后缀表达式。例如，后缀表达式 ["2"，"1"，"3"，"_"，"+"] 对应的算术表达式是 “2+1_3”，因此输出它的计算结果 5。

题的力扣地址 [1]

通过观察可以发现，适合用栈来计算后缀表达式。算法如下：

1.  遍历整个表达式。
    
2.  遇到数字，则入栈。
    
3.  遇到运算符，则取出栈顶的两个数字进行计算，并将结果入栈。
    

代码如下：

```
const evalRPN = function (tokens) {  var stack = new Stack();  for (let i = 0; i < tokens.length; i++) {    let token = tokens[i];    const isOperate = ['+', '-', '*', '/'].includes(token);    if (isOperate) {      const right = parseInt(stack.pop());      const left = parseInt(stack.pop());      let res;      switch (token) {        case '+':          res = left + right;          break;        case '-':          res = left - right;          break;        case '*':          res = left * right;          break;        case '/':          res = left / right;          break;      }      console.log(`${left} ${token} ${right} = ${res}`);      res = parseInt(res, 10); // 取整      stack.push(res);    } else {      stack.push(token);    }  }  return stack.pop();};
```

题 2 - 剑指 Offer II 037. 小行星碰撞
----------------------------

> 题目：输入一个表示小行星的数组，数组中每个数字的绝对值表示小行星的大小，数字的正负号表示小行星运动的方向，正号表示向右飞行，负号表示向左飞行。如果两颗小行星相撞，那么体积较小的小行星将会爆炸最终消失，体积较大的小行星不受影响。如果相撞的两颗小行星大小相同，那么它们都会爆炸消失。飞行方向相同的小行星永远不会相撞。求最终剩下的小行星。例如，有 6 颗小行星 [4，5，-6，4，8，-5]，如下图所示（箭头表示飞行的方向），它们相撞之后最终剩下 3 颗小行星 [-6，4，8]。

![](https://mmbiz.qpic.cn/mmbiz_jpg/mEakJnD74m8cIS9luz62b4XnTibRPzJkZcHoFgS4eicgjKT0qicicZs7Tg2x3H8dPiahFV0bTtRaCajjPSo5J3mH3lg/640?wx_fmt=jpeg)

题的力扣地址 [2]

小行星总是在数组的尾部碰撞，可以用栈来处理。

我们来分析下两个行星发生碰撞的情况。一共有四种情况：

1.  第一个：往左飞。第二个：往左飞。
    
2.  第一个：往左飞。第二个：往右飞。
    
3.  第一个：往右飞。第二个：往左飞。
    
4.  第一个：往右飞。第二个：往右飞。
    

第一种情况和第四种情况不会相撞，因为同方向飞不会相撞。第二种情况也不会相撞，因为是背着飞。只有第三种情况会撞。

基于上面的分析，算法如下：

1.  用栈来存碰撞完的结果。
    
2.  遍历数组。对当前的行星做如下处理。
    
3.  当栈顶的行星向右飞，当前的行星向左飞时，进行碰撞。其他情况将当前的行星入栈。
    
4.  碰撞逻辑是：
    

1.  当前的行星小于栈顶行星。什么都不做。
    
2.  当前的行星等于栈顶行星。栈顶行星出栈。
    
3.  当前的行星大于栈顶行星。栈顶行星出栈。当前的行星继续和下一个栈顶行星做碰撞。直到不碰撞或栈为空。
    

代码如下：

```
const asteroidCollision = function (asteroids) {  if (!asteroids) {    return [];  }  asteroids = asteroids.filter((item) => item !== 0);  if (asteroids.length === 0) {    return [];  }  const stack = [];  for (let i = 0; i < asteroids.length; i++) {    const curr = asteroids[i];    if (stack.length === 0) {      stack.push(curr);      continue;    }    // 碰撞    let isBig = false;    while (stack.length > 0) {      const last = stack[stack.length - 1];      // 不碰撞      if(!(last > 0 && curr < 0)) {        stack.push(curr);        break;      }      if (last > -curr) {        isBig = false;        break;      } else if (last === -curr) {        // 同归于尽        isBig = false;        stack.pop();        break;      } else {        // 继续碰撞        stack.pop();        isBig = true;        continue;      }    }    if(stack.length === 0 && isBig) {      stack.push(curr);    }  }  return stack;};
```

题 3 - 剑指 Offer II 038. 每日温度
---------------------------

> 题目：输入一个数组，它的每个数字是某天的温度。请计算每天需要等几天才会出现更高的温度。例如，如果输入数组 [35，31，33，36，34]，那么输出为 [3，1，1，0，0]。由于第 1 天的温度是 35℃，要等 3 天才会出现更高的温度 36℃，因此对应的输出为 3。第 4 天的温度是 36℃，后面没有更高的温度，它对应的输出是 0。其他的以此类推。

题的力扣地址 [3]

容易想到的算法是：遍历温度数组，每个温度都往后去找更高的温度。

用栈实现的算法比上面算法的时间复杂度低。算法如下：

1.  用栈来存当前没找到最更高值的温度下标。
    
2.  遍历温度数组。用当前的温度做如下处理。
    
3.  当前温度和栈顶元素比较，如果栈顶元素小于当前温度，则出栈，天数为下标的差值。当前温度不断和栈顶元素比较，直到当前温度不大于栈顶元素或栈为空。
    
4.  将当前温度入栈。
    

代码如下：

```
const dailyTemperatures = function (temperatures) {  if (!temperatures || !temperatures.length) return [];  let res = temperatures.map(() => 0);  let notFoundArr = [];  for (let currIndex = 0; currIndex < temperatures.length; currIndex++) {    const currValue = temperatures[currIndex];    while(notFoundArr.length > 0) {      const prevIndex = notFoundArr[notFoundArr.length - 1];      const prevValue = temperatures[prevIndex];      if (currValue > prevValue) {        res[prevIndex] = currIndex - prevIndex;        notFoundArr.pop();      } else {        break;      }    }    notFoundArr.push(currIndex);  }  return res;};
```

题 4 - 剑指 Offer II 039. 直方图最大矩形面积
--------------------------------

> 题目：直方图是由排列在同一基线上的相邻柱子组成的图形。输入一个由非负数组成的数组，数组中的数字是直方图中柱子的高。求直方图中最大矩形面积。假设直方图中柱子的宽都为 1。例如，输入数组 [3，2，5，4，6，1，4，2]，其对应的直方图如下图所示，该直方图中最大矩形面积为 12，如阴影部分所示。

![](https://mmbiz.qpic.cn/mmbiz_jpg/mEakJnD74m8cIS9luz62b4XnTibRPzJkZjMYMuk75jVtoRzewGmCngVSNET1zE8hrBN0RJAQBaCIQjGxdtky0xg/640?wx_fmt=jpeg)

题的力扣地址 [4]

我们知道，面积最大值包含的那矩形的高度，一定和某个柱子的高度相同。因此，找出最大的矩形面积，也就是找出以每个柱子的高度为高度的矩形中的最大值。

以某个柱子高度为高度的矩形面积为：`宽度 * 柱子高度`。`宽度 = 以某个柱子为中心往两边找相邻的，比它高的柱子的数量`。用 直接遍历 或 分治法 都很容易实现。

用栈来实现，时间复杂度比上面的算法低。具体算法如下：

1.  用栈来存高度升序排列的柱子的下标。
    
2.  遍历柱子数组。
    

1.  如果当前柱子高度小于栈顶柱子高度。则出栈。并计算以出栈元素为顶的矩形面积。
    
2.  重复上一步。直到当前柱子大于栈顶元素或栈为空。因此，栈中的柱子一定是按升序排列的。
    
3.  当前柱子的下标入栈。
    

4.  计算栈中以每个柱子为顶的矩形面积。
    

通过 2c 步 可知，所有柱子元素都会入栈。2a 步计算了所有出栈元素的矩形面积，第 3 步计算了所有未出栈元素的矩形面积。因此，也就计算了所有柱子为顶的高度的矩形面积。

该算法效率高效的原因是，算宽度的时间复杂度低。

*   步骤 2a 中，可知：当前柱子出栈后的栈顶柱子的高度比出栈柱子低，但当前柱子出栈后的栈顶柱子右侧的柱子高度不比出栈柱子低。因此矩形宽度为：`当前数组下标 - (当前元素出栈后的栈顶元素 + 1)`。算宽度的过程，不需要做遍历操作。
    
*   同理，步骤 3 中，矩形的宽度为：`数组长度 - (当前元素出栈后的栈顶元素 + 1)`。
    

代码如下：

```
const largestRectangleArea = function (heights) {  if (!heights || !heights.length) {    return 0;  }  let maxArea = 0;  const stack = [];  for (let i = 0; i < heights.length; i++) {    let currHeight = heights[i];    while (stack.length && currHeight < heights[stack[stack.length - 1]]) {      const heightIndex = stack.pop();      const height = heights[heightIndex];      const width = i - (stack.length ? stack[stack.length - 1] + 1 : 0);      const area = width * height;      maxArea = Math.max(maxArea, area);          }    stack.push(i);  }  // 计算栈中以每个柱子为顶的矩形面积。  while (stack.length) {    const heightIndex = stack.pop();    const height = heights[heightIndex];    const width =  heights.length - (stack.length ? stack[stack.length - 1] + 1 : 0);    const area = width * height;    maxArea = Math.max(maxArea, area);  }  return maxArea;};
```

题 5 - 剑指 Offer II 040. 矩阵中最大的矩形
-------------------------------

> 题目：请在一个由 0、1 组成的矩阵中找出最大的只包含 1 的矩形并输出它的面积。例如，在下图的矩阵中，最大的只包含 1 的矩阵如阴影部分所示，它的面积是 6。

![](https://mmbiz.qpic.cn/mmbiz_jpg/mEakJnD74m8cIS9luz62b4XnTibRPzJkZ51ETjZE37MibaYNrjLZ2Qib7tUuZuibeGkWy7rBQsHaqlMDPkoZ18U7fA/640?wx_fmt=jpeg)

题的力扣地址 [5]

将 1 看成柱子，将 0 看成是空白。将矩阵按行切成行的个数个子矩阵，计算所有的子矩阵中只包含 1 的矩形的面积，即可知道最大的面积。切后，每行最大面积就转化成了直方图最大矩形面积。可以上题的方式求解。

![](https://mmbiz.qpic.cn/mmbiz_jpg/mEakJnD74m8cIS9luz62b4XnTibRPzJkZ51ETjZE37MibaYNrjLZ2Qib7tUuZuibeGkWy7rBQsHaqlMDPkoZ18U7fA/640?wx_fmt=jpeg)

将上图的矩阵，按行可以切成下面的 4 个子矩阵：

![](https://mmbiz.qpic.cn/mmbiz_jpg/mEakJnD74m8cIS9luz62b4XnTibRPzJkZF9dhlEZtgyfDyAhdQLzbutE7BbF5eRONwPib8M6XiaiahuicCSwKeaY3SA/640?wx_fmt=jpeg)

代码如下：

```
const maximalRectangle = (matrix) => {  if (!matrix || !matrix.length) {    return 0;  }  const cols = [];  const colsLength = matrix[0].length;  for (let col = 0; col < colsLength; col++) {    cols[col] = [];  }  for (let row = 0; row < matrix.length; row++) {    for (let col = 0; col < colsLength; col++) {      cols[col][row] = matrix[row][col];    }  }  const maxArr = matrix.map((row, rowIndex) => {    const currCols = cols.map((c) => {      const currCol = c.slice(0, rowIndex + 1);      const height = getHeight(currCol);      return height;    });    return largestRectangleArea(currCols);  });  const max = Math.max(...maxArr);  return max;};function getHeight(arr) {  let height = 0;  for (let i = arr.length - 1; i >= 0; i--) {    if (arr[i] === '1') {      height++;    } else {      break;    }  }  return height;}
```

总结
--

在符合栈特点的场景下，用栈来求解可以降低算法的时间复杂度。难点在于，判断当前场景是否适合用栈。

相关阅读
----

*   [《剑指 Offer：专项突破版》 - 哈希表部分 JavaScript 题解](https://mp.weixin.qq.com/s?__biz=MzkwNzIyMTYzNA==&mid=2247485230&idx=1&sn=3181e3b7cee499befee5f5df6bcfb19b&scene=21#wechat_redirect)
    
*   [《剑指 Offer：专项突破版》 - 链表部分 JavaScript 题解](https://mp.weixin.qq.com/s?__biz=MzkwNzIyMTYzNA==&mid=2247485212&idx=1&sn=90b875360a588795621432715af25a8b&scene=21#wechat_redirect)
    
*   [《剑指 Offer：专项突破版》 - 数组部分 JavaScript 题解](https://mp.weixin.qq.com/s?__biz=MzkwNzIyMTYzNA==&mid=2247485091&idx=1&sn=d5036ae98b4e185a80a94a43cc2dd534&scene=21#wechat_redirect)
    
*   [《剑指 Offer：专项突破版》 - 字符串部分 JavaScript 题解](https://mp.weixin.qq.com/s?__biz=MzkwNzIyMTYzNA==&mid=2247485105&idx=1&sn=a7fbd017e697f05387899f7d23314fa2&scene=21#wechat_redirect)
    
*   [《剑指 Offer：专项突破版》 - 整数部分 JavaScript 题解](https://mp.weixin.qq.com/s?__biz=MzkwNzIyMTYzNA==&mid=2247485061&idx=1&sn=699aa93fe1ccd48862186578c115e754&scene=21#wechat_redirect)
    

### 参考资料

[1]

题的力扣地址: _https://leetcode-cn.com/problems/8Zf90G/_

[2]

题的力扣地址: _https://leetcode-cn.com/problems/XagZNi/_

[3]

题的力扣地址: _https://leetcode-cn.com/problems/iIQa4I/_

[4]

题的力扣地址: _https://leetcode-cn.com/problems/0ynMMM/_

[5]

题的力扣地址: _https://leetcode-cn.com/problems/PLYXKQ/_

 ![](http://mmbiz.qpic.cn/mmbiz_png/mEakJnD74m965xnGDuRc9yHa3d69a3nXZZO0qiaXjklno7xJqiaZ1tqotAEKOEBLNnbN3XB7zBpjNK5SzoqHyZibQ/0?wx_fmt=png) ** 前端 GoGoGo ** 致力于提升前端收入。提供有深度的面试，晋级提升的前端内容。 42 篇原创内容  公众号