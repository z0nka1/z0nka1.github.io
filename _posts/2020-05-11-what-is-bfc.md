---
layout: post
title: 什么是BFC？有什么作用？
summary: 本文讲解如何创建BFC，以及通过例子说明BFC的作用
featured-img: css
categories: css
---


BFC(Block Formatting Context)，块级格式上下文，CSS中的一个术语，表示的是一个Web页面中的一部分，用来放置block boxes。
很难理解是不是？让我们看看[W3C](http://www.w3.org/TR/CSS2/visuren.html#block-formatting)的定义：

>Floats, absolutely positioned elements, inline-blocks, table-cells, table-captions, and elements with ‘overflow’ other than ‘visible’ (except when that value has been propagated to the viewport) establish new block formatting contexts.

这么看总算有点明白了，但还不是很清楚。

让我们明确一下：

符合以下条件**之一**，就产生了一个BFC：

1. 浮动元素，即`float`的值不是`none`
2. 绝对定位的元素，即`position`的值是`absolute`或者`fixed`
3. `display`的值是`table-cell`,`table-caption`,`inline-block`,`flex`或者`inline-flex`
4. `overflow`的值不是`visible`

补充一点，`html`元素也是一个BFC。

为行文方便，以下内容在需要创建BFC时，统一使用`overflow: hidden`。

## BFC中盒子的对齐方式

来自W3C：

>In a block formatting context, each box’s left outer edge touches the left edge of the containing block (for right-to-left formatting, right edges touch). This is true even in the presence of floats (although a box’s line boxes may shrink due to the floats), unless the box establishes a new block formatting context (in which case the box itself may become narrower due to the floats).

![]({{site.url}}{{site.baseurl}}/assets/img/no_subject/bfc_1.png)

用直白的语言说就是，在同一个BFC中，每个盒子都靠左对齐（LTR格式情况下），他们的左外边框都紧紧挨着容器的左外边框。就算是左浮动的元素，它的左外边框也紧紧挨着容器的左外边框。

## BFC会造成margin折叠

准确地说应该是，同一个BFC中相邻的元素，他们的margin不是两者之和，而是取较大的一个。

```html
<div class="container">
  <p>Sibling 1</p>
  <p>Sibling 2</p>
</div>
```

```css
.container {
  background-color: red;
  overflow: hidden; /* 新创建一个BFC */
}

p {
  background-color: lightgreen;
  margin: 10px 0;
}
```

在这个例子中，我们显式的创建了一个BFC（这里不显式创建BFC也是可以的，因为任何元素都在`html`这个BFC里面），另外为`p`标签指定了`10px`的上下边距。

那么两个`p`元素之间的间距会是多少呢？

![]({{site.url}}{{site.baseurl}}/assets/img/no_subject/bfc_2.png)

答案不是`20px`，而是`10px`。

那如果不想让它折叠呢？可以通过新创建一个BFC来达到这个目的。

看看怎么做：

```html
<div class="container">
  <p>Sibling 1</p>
  <p>Sibling 2</p>
  <div class="newBFC">
    <p>Sibling 3</p>
  </div>
</div>
```

```css
.container {
  background-color: red;
  overflow: hidden;
}

p {
  margin: 10px 0;
  background-color: lightgreen;
}

.newBFC {
  overflow: hidden;  /* 新创建一个BFC */
}
```

上面代码中，通过为第三个元素新创建一个BFC，来避免第二个元素和第三个元素之间的margin折叠：

![]({{site.url}}{{site.baseurl}}/assets/img/no_subject/bfc_3.png)

## 用BFC来避免高度塌陷

当子级浮动的时候，容器的高度会塌陷，造成子级跑到容器外面去的现象。通过在容器上创建一个BFC，可以轻松解决这个问题。

![]({{site.url}}{{site.baseurl}}/assets/img/no_subject/bfc_4.png)

```html
<div class="container">
  <div>Sibling</div>
  <div>Sibling</div>
</div>
```

```css
.container {
  background-color: green;
}

.container div {
  float: left;
  background-color: lightgreen;
  margin: 10px;
}
```

这样，子级元素浮动，容器盒子会发生高度为0的情况（即高度塌陷），通常这并不是我们想要的。通过为容器指定`overflow: hidden`创建一个BFC来解决这个问题：

```css
.container {
  overflow: hidden; /* 创建一个BFC */
  background-color: green;
}

.container div {
  float: left;
  background-color: lightgreen;
  margin: 10px;
}
```

## 使用BFC来避免文字环绕

当我们把一个元素设置为左浮动的时候，它后面的元素中的文字就会环绕这个浮动元素展示，就像下图一一样。但是有时候我们不希望它环绕，就像下图二那样，应该怎么做？

![]({{site.url}}{{site.baseurl}}/assets/img/no_subject/bfc_5.png)

有一个方法是使用margin，但是也可以为承载文字的那个元素创建一个BFC，来达到这个目的。

```html
<div class="container">
  <div class="floated">
    Floated div
  </div>
  <p>
    Quae hic ut ab perferendis sit quod architecto, 
    dolor debitis quam rem provident aspernatur tempora
    expedita.
  </p>
</div>
```

```css
.container {
  width: 500px;
  min-height: 280px;
  border: 1px solid #aaa;
  padding: 10px;
}

.floated {
  float: left;
  margin: 5px;
}

p {
  margin: 0;
  text-align: left;
  background-color: green;
  color: white;
  padding: 10px;

  overflow: hidden; /* 创建一个BFC */
}
```

这样，文字所在元素就能变窄，不至于跑到浮动元素下面去了。

## 在多列布局中使用BFC来避免换行

当我们实现一种占满整个容器宽度的多列布局的时候，常常会由于浏览器对每列宽度进行四舍五入的缘故，造成总列宽大于实际容器宽度。这样的结果是最后一列会换行。当然我们可以用flex布局来解决这个问题，但是我们也可以通过使用BFC来避免最后一列换行。

```html
<div class="container">
  <div class="column">column 1</div>
  <div class="column">column 2</div>
  <div class="column column3">column 3</div>
</div>
```

```css

.column {
  width: 31.34%;
  padding: 10px 0;
  background-color: green;
  float: left;
  margin: 0 1%;
}

.column3 {
  float: none;
}
```

这样由于浏览器对宽度进行四舍五入计算的缘故导致最后一列换行了：

![]({{site.url}}{{site.baseurl}}/assets/img/no_subject/bfc_6.png)

通过为最后一列创建BFC，解决换行的问题：

```html
<div class="container">
  <div class="column">column 1</div>
  <div class="column">column 2</div>
  <div class="column column3">column 3</div>
</div>
```

```css

.column {
  width: 31.34%;
  padding: 10px 0;
  background-color: green;
  float: left;
  margin: 0 1%;
}

.column3 {
  float: none;
  overflow: hidden;
}
```

增加`overflow: hidden`后效果如下：

![]({{site.url}}{{site.baseurl}}/assets/img/no_subject/bfc_7.png)

CodePen见[这里](https://codepen.io/z0nka1/pen/ExVRadw)。


以上就是本文的全部内容了，希望对你有帮助，感谢阅读！