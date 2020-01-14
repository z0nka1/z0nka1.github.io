---
layout: post
title: JavaScript中变量是如何存储的
summary: JavaScript中有两种数据类型：原始类型和引用类型，那他们分别是怎么存储在内存中的呢？
featured-img: javascript
categories: javascript
---

先看一小段代码：

```javascript
let a = 1;
let b = a;
a = 2;
console.log(b);
```

结果输出1，这很简单！

再来看一个例子：

```javascript
let a = { age: 1 };
let b = a;
a.age = 2;
console.log(b);
```

这里结果输出什么呢？
结果是：{ age: 2 }

为什么这里改了a对象，b对象也跟着变了呢？

要回答这个问题，就要知道JavaScript中变量的存储方式。

我们知道，JavaScript中有两个大类的数据，分别的原始类型的数据和引用类型的数据。

原始类型的数据包含以下几种：

- Null
- Undefined
- Boolean
- String
- Number
- BigInt
- Symbol

引用类型的数据只有一种：

- Object

**原始类型的数据，存放在栈中，也就是调用栈。**

**引用类型的数据存放在堆中，在栈中仅仅保存该数据在内存中的地址。**这也回答了上面第二段代码的问题，因为a变量保存的是一个内存地址，所以当把a变量赋给b时，b也得到了这个内存地址。这样a和b都指向同一块内存地址，所以修改a的属性时，b也跟着被修改了。

![]({{site.url}}{{site.baseurl}}/assets/img/no_subject/stack_heap.png)

也许你很好奇，为什么引用类型的数据不能像原始类型一样存放在栈中呢？

因为JavaScript引擎需要用栈来维护程序执行期间上下文的状态，而引用类型的数据一般都比较大，如果放在栈中的话，会影响到执行上下文切换的效率，进而又影响到整个程序的执行效率。