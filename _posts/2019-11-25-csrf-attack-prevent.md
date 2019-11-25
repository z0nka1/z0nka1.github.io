---
layout: post
title: CSRF攻防
summary: 本文讲解CSRF攻击方式与防守策略
featured-img: code
categories: Web安全
---

浏览器虽然有同源策略的限制，但是为了增加灵活性，又允许页面加载任意第三方资源，而且通过CORS（跨域资源共享），站点又可以实现跨域获取数据。允许页面加载任意第三方资源给了XSS攻击可乘之机，而CORS又为CSRF攻击创造了条件。

## 概念

CSRF全称Cross-site request forgery，翻译过来就是跨站请求伪造。通过名称就可以知道，这种攻击就是第三方站点伪造目标站点（被攻击站点）的请求来发起攻击，那么怎么个伪造法呢？

## 攻击方法

攻击步骤是这样的：

1.用户登录目标站点（被攻击站点）并在浏览器保存了登录信息（如cookie、session等）；

2.用户点击黑客的链接或者访问黑客站点；

这么讲未免太抽象，不好理解，下面举例说明。

假设我这个博客网站有登录和钱包功能，而且还有付费内容，用户可以充值到自己的钱包以用于消费。而且还有转账功能，用户可以给好友转账，因此有一个转账接口：

```javascript
// 接口路径
https://z0nka1.github.io/transfer

// 参数
toUser
howMuch
```

这时黑客可以利用这个接口，把用户钱包的money转到自己名下：

黑客有三种方法可以达到目的。

### 方法一

引诱用户点击链接。

比如黑客在这个博客的某篇文章的评论区写道：

```
<a href="https://z0nka1.github.io/transfer?toUser=hacker&howMuch=1000">高清美女私房写真，速戳</a>
```

这样，某些抵挡不住诱惑的小伙伴就把自己钱包的money转给黑客了。

### 方法二

第二种方法是，自动get请求：

黑客在一个站点（比如www.hacker.com）放了这么一段代码：

```
<img src="https://z0nka1.github.io/transfer?toUser=hacker&howMuch=1000" />
```

然后用户在浏览器保存着`https://z0nka1.github.io/`登录状态的情况下访问`www.hacker.com`，浏览器会把它当做普通的图片资源下载，从而请求`https://z0nka1.github.io/transfer?toUser=hacker&howMuch=1000`这个路径，这样，用户的钱包的money就被转到黑客名下了。

### 方法三

第三种方法是，自动post请求：

跟方法二一样，黑客在`www.hacker.com`（这里只是举例哈）放了一段代码，只不过这次换为：

```

<!DOCTYPE html>
<html>
<body>
  <form id='hacker-form' action="https://z0nka1.github.io/transfer" method=POST>
    <input type="hidden" name="toUser" value="hacker" />
    <input type="hidden" name="howMuch" value="1000" />
  </form>
  <script> document.getElementById('hacker-form').submit(); </script>
</body>
</html>
```

当用户访问`www.hacker.com`这个站点，上面表单就会被自动提交，从而把1000块钱从用户账户钱包转到黑客账户的钱包。

说完了攻击方法，我们再来看看如何防范这种类型的攻击。

## 如何防范

### 方法一：sameSite

既然这种攻击需要用到登录信息，那我们就在登录信息上下手。

cookie是一个非常重要的登录信息，cookie有一个属性：sameSite，这个属性有三个值：

1.strict。如果sameSite设置为这个值，就表示这个cookie只能用于该站点，任何第三方站点的任何请求都不能携带该cookie。例如前面举的例子，如果这个博客的登录信息的cookie设置了sameSite=strict，那么第三方站点请求转账接口都不会携带该cookie，这样自然无法实施攻击。

2.lax。这个相对宽松一些，从第三方站点的链接打开和从第三方站点提交 Get 方式的表单这两种方式都会携带cookie。但如果在第三方站点中使用 Post 方法，或者通过 img、iframe 等标签加载的URL，这些场景都不会携带cookie。

3.none。设为这个值时任何情况都会携带该cookie。

### 方法二：Referer和Origin

HTTP请求头带着Referer和Origin属性，标记一个请求的来源。区别是Referer包含具体URL路径，而Origin只包含域名。这样服务器可以根据这两个值来判断一个请求的来源，从而过滤掉某些站点的请求。

### 方法三：CSRF token

因为CSRF攻击不像XSS，它不能获取目标站点页面数据，所以可以利用这一点，在服务器端生成一个token，然后返回给页面并植入在页面中。在页面请求服务器的时候就把这个token带上，这样服务器就可以根据这个token来判断是否是可信任的了。

好了，以上就是这篇文章的全部内容了，感谢阅读！