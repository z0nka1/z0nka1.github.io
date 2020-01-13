---
layout: post
title: 网站换肤的更好方案
summary: 通过link标签的title属性和切换disabled值，实现更好的用户体验更易于维护的网站换肤方案
featured-img: code
categories: html
---

说到网站换肤，大家第一时间想到的大概是改变class的方法，这也是比较常用比较为大家所熟知的。
但是这个方法有个缺点就是在大型项目中不容易维护。

大家可能还知道另外一个方法，那就是通过JS去改变CSS文件路径，这种方法也是行得通的，维护起来也不麻烦。
但还是有一个致命缺点，就是切换起来会感觉到卡顿，因为需要加载CSS文件数据。

以上两种方法都不完美，那还有没有更好的解决方案呢？

答案是肯定的。

下面就来介绍这更好的解决方案。

### 步骤一：给link标签指定title

我们知道，外部CSS一般是通过link标签来引入的。

```html
<link href="default.css" rel="stylesheet" type="text/css">
```

但是如果我们一次性引入多个样式文件，如果这些样式文件存在相同的规则，后引入的文件的规则就会把先引入的覆盖掉。

但是，如果我们给link标签指定一个title属性，情况就完全不一样了。

```html
<link href="default.css" rel="stylesheet" type="text/css" title="default">
<link href="blue.css" rel="stylesheet" type="text/css" title="blue">
<link href="grey.css" rel="stylesheet" type="text/css" title="grey">
```

就像这样，我们分别给两个link标签指定了不同的title，这样，放在后面的文件就会不起作用，只有第一个文件起作用。就像我们的例子，我们给两个link标签都指定了title属性，这时只有default.css起作用，blue.css和grey.css虽然也会加载，但是暂时不会被应用。

### 步骤二：通过设置link标签的disabled值实现样式切换

上一步，我们实现了引入多个样式文件，但只有部分文件被应用。
现在我们需要切换起作用的样式文件。如何才能做到这一点呢？
答案是disabled！
link标签有一个disabled属性（不是标签属性，是DOM节点的属性），通过这个属性，就可以设置一个link标签所引入的样式文件是否起作用。

```javascript
let link = document.querySelector("link[title='blue']");
link.disabled = false;
```

最终我们通过遍历link标签，把需要应用的样式表的disabled值设为false，其他样式表设为true，实现主体切换。

```html
<!DOCTYPE html>
<html>
<head>
	<title>demo</title>
	<link href="default.css" rel="stylesheet" type="text/css" title="default">
	<link href="blue.css" rel="stylesheet" type="text/css" title="blue">
	<link href="grey.css" rel="stylesheet" type="text/css" title="grey">
	<style>
		.container {
			width: 350px;
			height: 200px;
			text-align: center;
			line-height: 150px;
			margin: 20px auto;
		}
		.operate {
			width: 350px;
			margin: 0 auto;
		}
	</style>
</head>
<body>
	<div class="container">
		Pick a theme you like!
	</div>
	<div class="operate">
		<label><input name="theme" type="radio" value="default.css" checked />default</label>
		<label><input name="theme" type="radio" value="blue.css" />blue</label>
		<label><input name="theme" type="radio" value="grey.css" />grey</label>
	</div>
	<script>
		let links = document.querySelectorAll('link[title]');
		let radios = document.querySelectorAll('[type="radio"]');
		Array.from(radios).forEach(radio => {
			radio.addEventListener('click', () => {
				const value = radio.value;
				Array.from(links).forEach(link => {
					const href = link.getAttribute('href');
					link.disabled = true;
					if (href === value) {
						link.disabled = false;
					}
				})
			})
		});
	</script>
</body>
</html>
```

实现效果：

![]({{site.url}}{{site.baseurl}}/assets/img/no_subject/change_theme.gif)