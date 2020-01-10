---
layout: post
title: 不要再在flex布局中用width设置子项长度了
summary: 在flex布局中用width或者min-width设置子项长度，其实没有必要，用好flex属性，就可以实现相同效果
featured-img: css
categories: css
---

**注意：本文全部内容都基于flex布局方向为横轴的情况**

## 一，flex属性介绍

flex是flex-grow、flex-shrink和flex-basis的简称，可以把后三者写在一起。

下面介绍如何一眼看懂flex简写代表的是什么。

首先要明确flex-grow、flex-shrink和flex-basis的默认值分别是什么：

flex-grow：默认值0，表示就算有剩余空间**也不**变大。
flex-shrink：默认值1，表示空间不足时自动缩小以适应容器。
flex-basis：默认值auto，表示尺寸根据自身宽高进行调整。详细一点说就是，如果元素没有指定width、min-width和max-width，则元素基准长度由元素内容的长度决定，如果指定了则由指定长度决定。

然后再来看看flex简写属性在不同情况下代表什么。

### 一. 一个值，比如flex: 1

分两种情况：
1. 如果这个值是数字，那么这个值表示flex-grow，其他为默认值。例如`flex: 1`与`flex: 1 1 auto`等价。
2. 如果是flex-basis的可选值，如auto等，则该值表示flex-basis，其他为默认值。例如`flex: 200px`与`flex: 0 1 200px`等价。

### 二. 两个值，比如flex: 1 0

此时第一个值一定表示flex-grow。第二个值分两种情况：
1. 如果这个值是数字，则表示flex-shrink，此时flex-basis取默认值auto。例如`flex: 1 0`与`flex: 1 0 auto`等价。
2. 如果是flex-basis的可选值，则表示flex-basis，此时flex-shrink取默认值1。例如`flex: 1 200px`与`flex: 1 1 200px`等价。

### 三. 三个值，分别表示flex-grow、flex-shrink和flex-basis

下面通过具体例子看看为什么说管理子项长度不需要用到width之类的长度属性。

比如我们要实现这样一个效果，其中一个子项固定宽度，其他子项自动伸缩填满容器。

![]({{site.url}}{{site.baseurl}}/assets/img/no_subject/flex_1.gif)

由此很容易写出这样的代码：

```html
.container {
	display: flex;
}
.item {
	flex: 1 auto;
}
.one {
	flex: 0 0 auto;
	width: 200px;
}

<div class="container">
	<div class="item one"></div>
	<div class="item two"></div>
	<div class="item three"></div>
	<div class="item four"></div>
</div>
```

其实，这里的width只是起到间接作用，起直接作用的是flex-basis，只是flex-basis为auto的时候，长度按照width来定。

上面代码完全可以这么写：

```html
.container {
	display: flex;
}
.item {
	flex: 1 auto;
}
.one {
	flex: 0 0 200px;
}

<div class="container">
	<div class="item one"></div>
	<div class="item two"></div>
	<div class="item three"></div>
	<div class="item four"></div>
</div>
```

效果是一模一样的。

再举一个min-width的例子。
假设我们要实现这样一个效果：其中一个子项有一个300px的最小宽度，当有多余空间的时候，这个子项也会增大。

![]({{site.url}}{{site.baseurl}}/assets/img/no_subject/flex_2.gif)

由此写出这样的代码：

```css
.container {
	display: flex;
}
.item {
	flex: 1 auto;
}
.one {
	min-width: 300px;
}
```

但是，这样写更好：

```css
.container {
	display: flex;
}
.item {
	flex: 1 auto;
}
.one {
	flex: 1 0 300px;
}
```

OK，本文内容就到这里！