> 本文由 [简悦 SimpRead](http://ksria.com/simpread/) 转码， 原文地址 [zhuanlan.zhihu.com](https://zhuanlan.zhihu.com/p/231813098)

前言
--

在 ES6 规范中，引入了 class 的概念。使得 JS 开发者终于告别了，直接使用原型对象模仿面向对象中的类和类继承时代。

但是 JS 中并没有一个真正的 `class` 原始类型， `class` 仅仅只是对原型对象运用语法糖。所以，只有理解如何使用原型对象实现类和类继承，才能真正地用好 `class`。

类语法**不会**为 JavaScript 引入新的面向对象的继承模型。

### **class 类概念与语法**

ES5 之前不存在类的概念，创建对象使用的构造函数，通过 new 操作符来创建；

为使 JS 更像面向对象，ES6 版本引入 class 概念，其基本语法：

```
class Cat{
    constructor(name,age){
        this.name = name;
        this.age = age;
    }
    Say(){
        return '我的名字是' + this.name;
    }
}
var cat1 = new Cat('有鱼',2);
console.log(cat1.Say());//我的名字是有鱼
```

![](https://pic4.zhimg.com/v2-a26b31161906be135b207d0f15e27677_b.jpg)![](https://pic1.zhimg.com/v2-923cace79a19daedc6bff3f325dafc18_b.jpg)

代码解析：

① constructor 是一个构造函数方法，创建对象时自动调用该方法

② constructor 是默认存在的，可以省略，程序亦可以调用

③ this 指的是**实例化对象**

④类中声明的方法不能加 function 关键字

⑤方法之间不要用逗号分隔，否则会报错

**二、class 类与原型的关系**

class 类本质上就是一个函数，自身指向的就是构造函数，看代码：

```
console.log(typeof Cat);// function
console.log(Cat.prototype.constructor ===Cat);//true
```

class 类是构造函数的另一种写法，仍然存在 prototype 方法

```
console.log(Cat.prototype);//object
```

可以通过原型 prototype 修改类方法和新增方法

```
Cat.prototype.Say = function(){
    return return '我的名字是' + this.name+',我是原型prototype声明同样的Say方法，把原有Say方法覆盖了';
}
cat2 = new Cat('年年',5);
console.log(cat2.Say());//我的名字是年年，我是原型prototype声明同样的Say方法，把原有Say方法覆盖了
Cat.prototype.Go = function(){
    return return '我的年龄是' + this.age;
}
console.log(cat2.Go());//我的年龄是5
```

![](https://pic2.zhimg.com/v2-a2aa3cbd7fbf7dd8f307844f7517a69d_b.jpg)

还可以通过 Object.assign 方法来为对象动态增加方法

```
Object.assign(Cat.prototype,{
    Eat:function(){
        return this.name;
    },
    Run:function(){
        return this.age;
    }
})
cat3 =new Cat('卡卡',4);
console.log(cat3.Eat());//卡卡
console.log(cat3.Run());//4
```

![](https://pic3.zhimg.com/v2-dd1c4402111d5fd6cbc4ed7c3848c886_b.jpg)

也可以使用实例对象的__proto__属性新增类的方法

```
cat3 =new Cat('卡卡',4);
cat4 =new Cat('楼楼',10);
cat3.__proto__.Play = function(){
    return this.name;
}
console.log(cat3.Play());// 卡卡
console.log(cat4.Play());// 楼楼
```

![](https://pic3.zhimg.com/v2-3f979572433f4955ce12c2535cb48a32_b.jpg)

**实例属性和原型属性**
-------------

![](https://pic3.zhimg.com/v2-1034cec14d857f5945eacf917aba583e_b.jpg)

实例属性：constructor 里面的属性为实例属性，即定义在 this 对象上

原型属性：除去实例属性都称为原型属性，即定义在 class 类上

hasOwnProperty 方法：可以通过 hasOwnProperty() 方法进行判断属性是否是实例属性

in 操作符：能够访问到属性时返回 true, 无论是实例属性还是原型属性

```
class Person(){
    constructor(per1,per2){
        this.per1 = per1;
        this.per2 = per2;
    }
    Say(){
        return per1+per2;
    }
}
var box=new Person('年年','有鱼');
console.log(Person.hasOwnProperty("per1"));//true
console.log(Person.hasOwnProperty("per2"));//true
console.log(Person.hasOwnProperty("Say"));//false
console.log("per1" in Person);//true
console.log("per2" in Person);//true
console.log("Say" in Person);//true
console.log("Go" in Person);//false
```

![](https://pic4.zhimg.com/v2-07cf5e5b4778d8ca218517f760b3620f_b.jpg)

**class 类的继承**
--------------

> **通过 extends 关键字实现类的继承**

```
class Person{
    constructor(name,age){
        this.name = name;
        this.age = age;
    }
    getName(){
        return this.name;
    }
    getAge(){
        return this.age; 
    }
}
class Student extends Person{
    getName(){
        return '我覆盖了父级的方法,'+ this.name;
    }
    getScore(){
        return '我是子级新增的方法,'+this.name;
    }
}
var stu1 = new Student('有鱼',2);
console.log(sut1.getName());// 我覆盖了父级的方法,有鱼
console.log(sut1.getAge());//2
console.log(sut1.getScore());// 我是子级新增的方法,有鱼
```

![](https://pic4.zhimg.com/v2-d82865328db650d7e9b2ec654434e267_b.jpg)

> **通过 super 关键字进行拓展父类构造器或方法**

super 作用：

①子类使用构造器 constructor 的时候，必须使用 super 关键字，用来扩展构造器

②子类同名方法会覆盖父类同名方法，使用 super 关键字后则可以调用到父类的同名函数

```
class Person{
    constructor(name){
        this.name = name;
    }
    getName(){
        console.log('我是父级类getName方法输出来的');
    }
    getAge(){
        console.log(this.age); 
    }
}
class Student extends Person{
    constructor(name,age,sex){
        super();//必须先调用super，才能使用constructor，才能使用this对象
        this.name = name;
        this.age = age;
        this.sex = sex;
    }
    getName(){
        super.getName();//调用super，才能调用父类同名函数getName
        console.log('我是子级类getName方法输出来的');
    }
}
var stu1 = new Student('有鱼',2);
stu1.getName();
// 我是父级类getName方法输出来的
// 我是子级类getName方法输出来的
```

![](https://pic3.zhimg.com/v2-dac905354310341abfc34283432a3002_b.jpg)

**static 关键字的使用**

①static 关键字是类的方法

②只能通过类名来调用，不能被实例对象调用

③static 方法也可以被继承

```
class Person{
    constructor(name,age){
        this.name = name;
        this.age = age;
    }
    static getAge(){
        console.log('我是静态属性static');
    }
}
class Student extends Person{}
var stu1 = new Student('有鱼',2);
Person.getAge();//我是静态属性static
Student.getAge();//我是静态属性static
stu1.getAge();//stu1.getAge is not a function
```

![](https://pic3.zhimg.com/v2-7fe3c8133c97de4e528dcdbe44c1117e_b.jpg)

ES6：`class`
-----------

通过类来创建对象，使得开发者不必写重复的代码，以达到代码复用的目的。它基于的逻辑是，两个或多个对象的结构功能类似，可以抽象出一个模板，依照模板复制出多个相似的对象。就像自行车制造商一遍一遍地复用相同的蓝图来制造大量的自行车。

使用 ES6 中的 `class` 声明一个类，是非常简单的事。它的语法如下:

```
class Person {
  constructor(name){
    this.name = name
  }

  hello(){
    console.log('Hello, my name is ' + this.name + '.');
  }
}

var xiaoMing = new Person('xiaoMing');
xiaoMing.hello() // Hello, my name is xiaoMing.
```

`xiaoMing` 是通过类 `Person` 实例化出来的对象。对象 `xiaoMing` 是按照类 `Person` 这个模板，实例化出来的对象。实例化出来的对象拥有类预先订制好的结构和功能。

ES6 的语法很简单，但是在实例化的背后，究竟是什么在起作用呢？

`class` 实例化的背后原理
----------------

使用 `class` 的语法，让开发者告别了使用 `prototype` 模仿面向对象的时代。但是，`class` 并不是 ES6 引入的全新概念，它的原理依旧是原型继承。

### typeof `class` == "function"

通过类型判断，我们可以得知，`class` 的并不是什么全新的数据类型，它实际只是 `function` (或者说 `object`)。

```
class Person {
  // ...
}

typeof Person // function
```

![](https://pic4.zhimg.com/v2-5a046dca812f7cd06b166a2d4f06270f_b.jpg)

`Person` 的属性并不多，除去用 `[[...]]` 包起来的内置属性外，大部分属性根据名字就能明白它的作用。需要我们重点关注的是 `prototype` 和 `__proto__` 两个属性。

(关于 `__proto__` 可以在[本文的姊妹篇](https://segmentfault.com/a/1190000008293372#articleHeader2) 找到答案)

### 实例化的原理： `prototype`

先来讲讲 `prototype` 属性，它指向一个特殊性对象：**原型对象**。

原型对象所以特殊，是因为它拥有一个普通对象没有的能力：将它的属性共享给其他对象。

在 ES6 规范 中，对 **原型对象** 是如下定义的：

```
object that provides shared properties for other objects
```

原型对象是如何将它的属性分享给其他对象的呢？

这里使用 ES5 创建一个类，并将它实例化，来看看它的实质。

```
function Person() {
  this.name = name
}

// 1. 首先给 Person.prototype 原型对象添加了 describe 方法 。
Person.prototype.describe = function(){
  console.log('Hello, my name is ' + this.name + '.');
}

// 2. 实例化对象的 __proto__ 指向 Person.prototype
var jane = new Person('jane');
jane.__proto__ === Person.prototype;


// 3. 读取 describe 方法时，实际会沿着原型链查找到 Person.prototype 原型对象上。
jane.describe() // Hello, my name is jane.
```

[人类身份验证 - SegmentFault](https://segmentfault.com/a/1190000008338987)

[类​developer.mozilla.org![图标](https://pic4.zhimg.com/v2-72382e605ce3eba8154aa310a712c403_ipico.jpg)](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Classes) [人类身份验证 - SegmentFault​segmentfault.com](https://segmentfault.com/a/1190000011783384)[Javascript 定义类（class）的三种方法 - 阮一峰的网络日志​www.ruanyifeng.com![图标](https://pic3.zhimg.com/v2-38588301a92a7e721ba7b383222e6cca_180x120.jpg)](http://www.ruanyifeng.com/blog/2012/07/three_ways_to_define_a_javascript_class.html)[- 掘金​juejin.im](https://juejin.im/entry/6844903495783415822)[- 掘金​juejin.im](https://juejin.im/entry/6844903495783415822)