---
layout: post
title: 多个链接如何用同一标签页打开或刷新
summary: 一个页面内的多个链接，如果href的URL相同，通过配置target，可以实现多个链接共用同一标签页。
featured-img: code
categories: html
---

如何只通过a标签，实现在多个标签的URL相同的时候，点击标签打开链接，如果这个链接没有打开，则打开一个新标签页；如果这个链接已经打开，那么就在已经打开的标签页刷新页面？

我们都知道，a标签的target属性可以是以下几个值之一：

1. _self：在当前标签页打开链接，这个是默认值
2. _blank：在新标签页打开链接
3. _parent
4. _top


**但我们不知道的是，target还可以是任意一个字符串！**

由此，**通过为不同a标签的target指定一个相同的字符串**，就可以实现我们的目标。

```html
<a href="./page.html" target="page">链接一</a>
<a href="./page.html?q=1" target="page">链接二</a>
<a href="./page.html?q=2" target="page">链接三</a>
```

>我这里在URL加查询参数只是为了容易区分。

就像这样，当第一次点击任意一个链接的时候，会打开一个新标签页；保持新打开的标签页为打开状态，回到放置链接的页面，点击另外一个标签，这时浏览器是在上次打开的标签页刷新，而不是再次新开一个标签页。

![]({{site.url}}{{site.baseurl}}/assets/img/no_subject/a_tag.gif)

通过这个小技巧，可以避免打开重复的相同页面，提升一点用户体验。
