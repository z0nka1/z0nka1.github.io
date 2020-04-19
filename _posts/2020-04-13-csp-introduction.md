---
layout: post
title: CSP（Content-Security-Policy）介绍
summary: Content-Security-Policy（以下简称CSP）中文翻译为内容安全策略，用来提高网页安全性。
featured-img: code
categories: Web安全
---


## 什么是Content-Security-Policy？

Content-Security-Policy（以下简称CSP）中文翻译为内容安全策略，用来提高网页安全性。它可以允许你限制资源（例如JavaScript、CSS、图片等）怎么加载，其实就是一个白名单机制，告诉浏览器哪些资源可以加载，哪些不行。
虽然 CSP 主要用在HTTP header，但是你也可以把它写在HTML的元数据标签（meta tag）。

## CSP可以降低哪种类型的攻击风险？

最开始 CSP 是为了降低XSS（Cross Site Scripting）攻击风险而诞生的，但是随着版本更新，现在它也可以降低其他类型的攻击风险，比如点击劫持（[ClickJacking](https://www.imperva.com/learn/application-security/clickjacking/)）。

## CSP如何使用

第一种方法，你可以把它用在HTTP header里面：

```
Content-Security-Policy: default-src 'self'; script-src 'self' *.example.com;
```

另一种方法，用在HTML 元数据标签：

```html
<meta http-equiv="Content-Security-Policy" content="default-src 'self'; script-src 'self' *.example.com;">
```

## CSP指令介绍

CSP 有大概20个指令，每个指令间用半角分号`;`分隔。下面将介绍其中几个常用指令。

### default-src

`default-src`指令用来设置一系列指令的默认值。简单来说，可以用`default-src`来设置默认值的指令都带`-src`后缀。

下面这些指令**能**用`default-src`来设置默认值：

- script-src
- style-src
- font-src
- img-src
- connect-src
- worker-src
- object-src
- media-src
- manifest-src
- frame-src
- prefetch-src
- child-src

下面这些指令**不能**用`default-src`来设置默认值：

- base-uri
- form-action
- frame-ancestors
- report-uri
- sandbox

`default-src`的使用例子：

```
default-src 'self' cdn.example.com;
```

使用`default-src`的时候有一个注意点，那就是它不会被继承。看下面例子：

```
default-src 'self' cdn.example.com; script-src js.example.com;
```

在这个例子中，`'self'`和`cdn.example.com`并不会被继承到`script-src`中，`script-src`的值`js.example.com`会覆盖掉`'self'`和`cdn.example.com`。所以，在这个例子中，对于`script-src`来说，`default-src`就像不存在一样。

### script-src

该指令定义JavaScript的可用来源。
使用举例：
```
script-src js.example.com;
```

### style-src

该指令定义样式表的可用来源。
使用举例：
```
style-src css.example.com;
```

### img-src

该指令定义图像的可用来源。
使用举例：
```
img-src img.example.com;
```

### style-src

该指令定义样式表的可用来源。
使用举例：
```
style-src css.example.com;
```

### connect-src

该指令定义`XMLHttpRequest`，`WebSocket`和`EventSource`的可用请求地址。如果请求不被允许的地址，浏览器会模拟一个`400`的HTTP请求错误码。
使用举例：
```
connect-src 'self;
```

### font-src

该指令定义字体的可用来源（通过@font-face来加载的）。
使用举例：
```
font-src font.example.com;
```

### object-src

该指令定义插件的可用来源，比如`<object>`,`<embed>`或`<applet>`。
使用举例：
```
object-src 'self';
```

### media-src

该指令定义视频或音频的可用来源，比如 HTML5 的`<audio>`或`<video>`标签。
使用举例：
```
media-src media.example.com;
```

## 指令值解释

所有`-src`结尾的指令都有一组相似的值，多个值之间可以通过空格来分隔，只有一个例外，那就是`none`，如果指定了`none`，那就只能有一个值。

| 值 | 举例 | 描述 |
| ---- | ---- | ---- |
| `*` | `img-src *` | 通配符，允许通过除data: blob: filesystem: 外的所有URL加载资源 |
| `'none'` | `object-src 'none'` | 所有来源都不允许 |
| `'self'` | `script-src 'self'` | 允许通过所有同源（即相同协议、主机和端口号）URL加载资源 |
| `data:` | `img-src data:` | 允许通过data schema（比如Base64格式的图片）加载资源 |
| `domain.example.com` | `img-src domain.example.com` | 允许通过给定的域名加载资源 |
| `*.example.com` | `img-src *.example.com` | 允许通过`example.com`的所有二级域名加载资源 |
| `https:` | `script-src *.example.com` | 只允许通过`https`协议加载资源 |
| `'unsafe-inline'` | `script-src 'unsafe-inline'` | 允许通过内联元素比如`style`属性或者`script`标签加载资源 |
| `'unsafe-eval'` | `script-src 'unsafe-eval'` | 允许通过比如JavaScript的`eval()`方法加载资源 |
| `'nonce-'` | `script-src 'nonce-random'` | 指定一个随机字符串`random`，如果`script`标签也有`nonce`属性，并且属性值与指定的`random`字符串相同，那么这个`script`标签里面的代码就可以执行 |


OK，以上就是本文的全部内容了，感谢阅读！