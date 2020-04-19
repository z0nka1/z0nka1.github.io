---
layout: post
title: 清除浮动：几种方式及优劣对比
summary: 清除浮动很常用对吧？那你应该好好看看这篇文章！
featured-img: css
categories: css
---


## 什么是浮动？

浮动就是为元素指定CSS属性`float: left`或者`float: right`，使其漂浮于容器的左边或右边。

一开始，引入`float`属性是为了实现简单的布局，比如文字环绕图像的效果，就像很多印刷品排版那样：

![]({{site.url}}{{site.baseurl}}/assets/img/no_subject/clearing_floats_float.jpg)

很快，Web开发人员意识到浮动不仅可以应用于图像，还可以用在其他任何元素，于是浮动的适用范围扩大了，比如创建多列布局。

但是，由于浮动的特性，使得某些时候的行为并不是我们想要的：

![]({{site.url}}{{site.baseurl}}/assets/img/no_subject/clearing_floats_no_clear.jpg)

在这种情况下，我们需要调整它们的位置，让它们“正常”一点。

因此，清除浮动应运而生。

## 什么是清除浮动？

清除浮动就是，通过某些手段，使得特定元素回到正常流之中，就是所谓的看起来“正常”一些。

![]({{site.url}}{{site.baseurl}}/assets/img/no_subject/clearing_floats_cleared.jpg)

CSS的`clear`属性，就是专门用来清除浮动的。它可以用在浮动元素，也可以用在非浮动元素。

`clear`属性常用取值：

- `left`：当前元素的左边不允许有浮动元素。结果就是，如果当前元素的前一个元素是左浮动的，那么当前元素会挪到下一行。
- `right`：当前元素的右边不允许有浮动元素。结果就是，如果当前元素的前一个元素是右浮动的，那么当前元素会挪到下一行。
- `both`：当前元素的左边和右边都不允许有浮动元素。

## 清除浮动的几种方式

### 1. 利用空标签

```html
<div class="container">
  <div class="float">浮动的元素</div>
  <div class="float">浮动的元素</div>
</div>

<div class="footer">底部的内容</div>
```

```css
.float {
  float: left;
}
```

没有清除浮动，底部的内容出现在了它不应该出现的位置，而且容器的高度也没有被撑开：

![]({{site.url}}{{site.baseurl}}/assets/img/no_subject/clearing_floats_no_clear.jpg)

现在我们在浮动元素后面添加一个空标签，并为空标签设置`clear: both`来清除浮动：

```html
<div class="container">
  <div class="float">浮动的元素</div>
  <div class="float">浮动的元素</div>
  <div class="clear"></div>
</div>

<div class="footer">底部的内容</div>
```

```css
.float {
  float: left;
}

.clear {
  clear: both;
}
```

这样，我们的布局看起来就“正常”了：

![]({{site.url}}{{site.baseurl}}/assets/img/no_subject/clearing_floats_cleared.jpg)

这种方法简单直白，但是添加了一个无意义的空标签，就语义上来说不是很好。


### 2. 利用`overflow`

这种方法是在容器上设置一个`overflow`属性，值可以是`hidden`、`auto`或`scroll`，来创建一个[BFC](https://z0nka1.github.io/what-is-bfc/)，从而达到清除浮动的效果。

```html
<div class="container">
  <div class="float">浮动的元素</div>
  <div class="float">浮动的元素</div>
</div>

<div class="footer">底部的内容</div>
```

```css
.float {
  float: left;
}

.container {
  overflow: hidden;
}
```

这种方法比上一种稍好，没有添加无意义的空标签，但还是有个缺陷，那就是溢出的内容会被裁剪掉。如果你使用`auto`或`scroll`，滚动条也会成为一个恼人的存在。

### 3. 利用伪元素

这种方法巧妙利用伪元素`::before`和`::after`以达到清除浮动的目的：

```html
<div class="container clearfix">
  <div class="float">浮动的元素</div>
  <div class="float">浮动的元素</div>
</div>

<div class="footer">底部的内容</div>
```

```css
.float {
  float: left;
}

.clearfix:before,
.clearfix:after {
  content: "";
  display: table;
}

.clearfix:after {
  clear: both;
}

.clearfix {
  zoom: 1; /* ie 6/7 */
}
```

> tips：`::after`语法是CSS3引入的，而CSS2语法是`:after`这样的，支持`::after`的浏览器同时也支持`:after`，所以这里使用`:after`的语法。而且，IE8只支持`:after`这种语法。

这种方法没有添加额外的无意义的空标签，也不用担心溢出被裁剪或者滚动条的问题，推荐使用这种方法。

而且，如果你不需要兼容IE8以下的浏览器，可以简化成这样：

```css
.clearfix:after {
  content: "";
  display: table;
  clear: both;
}
```

## 总结

这篇文章中我们首先介绍了什么是浮动和清除浮动，接着介绍了三种清除浮动的方法：第一种方法是使用设置了`clear: both`的空标签，这种方法添加了无意义的空标签，所以不是很推荐这种方法；
第二种方法是使用`overflow`属性，这种方法有可能会使内容被裁剪，或者出现滚动条，同样不是推荐的做法；第三种方法是利用伪元素，这种方法总体上来说没什么缺点，可以当做公共类全局使用，而且如果不需要支持IE8以下的浏览器，CSS也可以写得很简洁，是目前最推荐的做法。

好了，以上就是本文的全部内容了，感谢阅读！