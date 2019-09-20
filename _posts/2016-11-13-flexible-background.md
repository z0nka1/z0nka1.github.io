---
layout: post
title: CSS3随内容自动伸缩的背景
summary: CSS3给我们带来一个非常实用的新属性：border-image，利用这个属性我们可以做出随着内容的增减自动伸缩的背景。
featured-img: emile-perron-190221
categories: CSS
---

CSS3给我们带来一个非常实用的新属性：border-image，利用这个属性我们可以做出随着内容的增减自动伸缩的背景。废话不多说，看代码！
HTML:
```html
<ol>
  <li>第一条列表内容</li>
  <li>第二条列表内容</li>
  <li>第三条列表内容</li>
  <li>第四条列表内容</li>
  <li>第五条列表内容</li>
</ol>
```
CSS：
```css
border: 20px solid;
width: 200px;
-webkit-border-image: url(border.png) 30 30 round;
```
效果图：

![]({{site.url}}{{site.baseurl}}/assets/img/flexible_background/img_1.jpg)


现在我们增加列表条目，看看效果图：

![]({{site.url}}{{site.baseurl}}/assets/img/flexible_background/img_2.jpg)


随着列表数目的增加，背景自动变大了，很好！
这是border.png：

![]({{site.url}}{{site.baseurl}}/assets/img/flexible_background/img_3.png)


你可能会对border-image中的30 30有疑问：
` -webkit-border-image: url(border.png) 30 30 round;`
看下图：

![]({{site.url}}{{site.baseurl}}/assets/img/flexible_background/img_4.png)


上面border-image中的两个数值，个人理解是，第一个表示图片的上下从边缘开始“吃”进多少像素作为边框，对应的，第二个数值表示左右。剩下的中间区域就会被重复（或者拉伸）作为背景。
你可能还有一个疑问，描边的大小：` border: 20px solid;`
我们看看描边为50像素时候的样子：

![]({{site.url}}{{site.baseurl}}/assets/img/flexible_background/img_5.jpg)



所以现在你明白了吧。
至于描边的颜色，因为不会显示出来，所以写不写无所谓。
好了，现在你已经知道了如何做出这种随内容自动伸缩的背景，可以去实践一下了！
原创文章，转载请注明出处！