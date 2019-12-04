---
layout: post
title: JavaScript：变量提升是怎么回事
summary: 为什么在JavaScript中变量可以先使用后定义而不报错？
featured-img: javascript
categories: JavaScript
---

先看以下代码，思考下打印结果是什么：

```javascript
console.log(name);
var name = 'jerry';
```

当执行到第一行的时候，这时`name`还未定义，所以应该会报错。

**真的是这样吗？**

事实是，这里会打印`undefined`，而不是报错。

为什么？

要解释这种现象，你需要了解“变量提升”。

**所谓变量提升，就是JavaScript引擎把变量的声明部分提到了代码开头，并且给变量设置默认值为`undefined`的现象。**

所以上面的代码，在JavaScript引擎看来是这样的：

```javascript
var name = undefined;
console.log(name);
name = 'jerry';
```

所以最后打印结果是`undefined`。

其实上面“JavaScript引擎把变量的声明部分提到了代码开头”这种说法并不准确。实际上，变量在代码里的位置是不会改变的，而且是在编译阶段被JavaScript引擎放到了内存中。

下面详细的展开说明。

在**编译阶段**，上面这段代码被分成了两部分：**执行上下文**和**可执行代码**。

执行上下文：

```javascript
// 这部分会保存在执行上下文的“变量环境”中
var name = undefined;
```

可执行代码：

```javascript
console.log(name);
name = 'jerry';
```

在**执行阶段**，JavaScript引擎开始执行“可执行代码”，当执行到`console.log(name)`的时候，JavaScript引擎就在**变量环境**中查找该变量，找到该变量并且发现其值为`undefined`，于是打印出`undefined`；然后接续往下执行，执行到`name = 'jerry'`，这是一个赋值操作，于是把值`jerry`赋给`name`。

这就是JavaScript中“变量提升”的具体工作原理了。

以上就是本文的全部内容了，感谢阅读！