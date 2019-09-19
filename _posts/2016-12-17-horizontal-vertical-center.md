---
layout: post
title: CSS水平垂直居中的几种方法
summary: 四十几年前人类已经登上了月球，但是直到今天，在CSS中还不能很好的实现水平垂直居中。
featured-img: emile-perron-190221
categories: CSS
---

# 一、脱离文档流元素的居中

## 方法一：margin:auto 法

CSS 代码：

```
div{
  width: 300px;
  height: 300px;
  position: relative;
  border: 1px solid #465468;
}
img{
  position: absolute;
  margin: auto;
  top: 0;
  left: 0;
  right: 0;
  bottom: 0;
}
```

HTML 代码：

```
<div>
   <img src="prince.png">
</div>
```

效果图：

![]({{site.url}}{{site.baseurl}}/assets/img/horizontal_vertical_center/img_1.png)

当一个元素绝对定位时，它会根据第一个不是 static 定位的祖先元素定位，因此这里的 img 根据外层 div 定位。

## 方法二：负 margin 法

CSS 代码：

```
.container{
      width: 500px;
      height: 400px;
      border: 2px solid #379;
      position: relative;
 }
 .inner{
      width: 480px;
      height: 380px;
      background-color: #746;
      position: absolute;
      top: 50%;
      left: 50%;
      margin-top: -190px; /*height的一半*/
      margin-left: -240px; /*width的一半*/
 }
```

HTML 代码：

```
<div class="container">
  <div class="inner"></div>
</div>
```

效果图：

![]({{site.url}}{{site.baseurl}}/assets/img/horizontal_vertical_center/img_2.png)

这里，我们首先用 top:50%和 left:50%让 inner 的坐标原点（左上角）移动到 container 的中心，然后再利用负 margin 让它往左偏移自身宽的一半，再往上偏移自身高的一半，这样 inner 的中心点就跟 container 的中心点对齐了。

# 二、未脱离文档流元素的居中

## 方法一：table-cell 法

CSS 代码：

```
div{
  width: 260px;
  height: 230px;
  border: 3px solid #555;
  display: table-cell;
  vertical-align: middle;
  text-align: center;
}
img{
  vertical-align: middle;
}
```

HTML 代码：

```
<div>
  <img src="prince.png">
</div>
```

效果图：

![]({{site.url}}{{site.baseurl}}/assets/img/horizontal_vertical_center/img_3.png)

div 上面的 vertical-align: middle 是控制垂直方向上的居中的，而 text-align: center 是控制水平方向的。一个有趣的事实是，当我们去掉 img 的 vertical-align: middle 之后，是这样的：

![]({{site.url}}{{site.baseurl}}/assets/img/horizontal_vertical_center/img_4.png)

还是居中啊！真的居中吗？
我们看到，图片往上移了一点，在垂直方向上已经不居中了。为什么？我也不知道，如果你知道，可以告诉我，不胜感激。
但是如果我们把图片换成文字：
CSS 代码：

```
div{
  border: 3px solid #555;
  width: 300px;
  height: 200px;
  display: table-cell;
  vertical-align: middle;
  text-align: center;
}
span{
  vertical-align: middle;
}
```

HTML 代码：

```
<div>
  <span>这是放在span中的文字，通过外层div设置display: table-cell以及vertical-align: middle实现垂直居中。</span>
</div>
```

效果图：

![]({{site.url}}{{site.baseurl}}/assets/img/horizontal_vertical_center/img_5.png)

当我们把 span 的 vertical-align: middle 去掉之后是这样的：

![]({{site.url}}{{site.baseurl}}/assets/img/horizontal_vertical_center/img_6.png)

看到差别没？文字的行间距更小了。如果你在自己电脑上运行代码就会发现，这几行字是向中间靠了，而并没有像图片一样往上移。我也在想办法搞清楚这是怎么回事，如果你知道原因，也烦请告诉我。

## 方法二：弹性盒子法

CSS 代码：

```
.container{
  width: 300px;
  height: 200px;
  border: 3px solid #546461;
  display: -webkit-flex;
  display: flex;
  -webkit-align-items: center;
  align-items: center;
  -webkit-justify-content: center;
  justify-content: center;
}
 .inner{
  border: 3px solid #458761;
  padding: 20px;
}
```

HTML 代码：

```
<div class="container">
  <div class="inner">
    我在容器中水平垂直居中
  </div>
</div>
```

效果图：

![]({{site.url}}{{site.baseurl}}/assets/img/horizontal_vertical_center/img_7.png)

align-items 控制垂直方向的居中，justify-content 控制水平方向的居中。这是 CSS3 的新属性，浏览器支持情况如下：

![]({{site.url}}{{site.baseurl}}/assets/img/horizontal_vertical_center/img_8.png)

以上！
