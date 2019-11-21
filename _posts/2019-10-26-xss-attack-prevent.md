---
layout: post
title: XSS攻防
summary: 本文讲解XSS攻击方式与防守策略
featured-img: code
categories: Web安全
---

我们都知道，页面可以引用任意第三方资源，比如`script`标签和`img`标签。还有CORS策略的存在，使页面可以轻松实现跨域请求资源。本来这些都是为了增加Web的灵活性，但是也很容易被攻击者利用，XSS攻击就是其中的典型。

### 什么是XSS攻击
XSS全称Cross Site Scripting，翻译过来就是跨站脚本的意思，写成XSS是为了与CSS区分开。XSS攻击就是攻击者将恶意脚本注入到页面中，并在用户浏览页面的时候对用户实施攻击的一种手段。

XSS攻击可以做这些事情：
1. 窃取用户cookie。利用这些cookie可以模拟用户登录。
2. 监听键盘等输入设备的事件，以获取用户输入，比如银行卡密码等。
3. 修改DOM。比如生成假的登录页面、往页面插入广告等。

这只是一部分。可以看到，通过XSS，攻击者能做很多对用户有害的事情，必须尽量避免。

接下来我们看看XSS都有哪些攻击方式，这样才能对症下药。

### XSS攻击方式

XSS攻击方式主要有**存储型XSS攻击**和**反射型XSS攻击**两种，下面一一讲解。

#### 1.存储型XSS攻击

这类攻击是指，攻击者将恶意脚本存储到目标服务器，当页面请求该服务器的资源时，恶意脚本被返回给页面并执行。

举个例子：
我在个人简介这里输入恶意脚本，然后保存：
![]({{site.url}}{{site.baseurl}}/assets/img/no_subject/save_script.png)

然后当别人访问我的个人页面的时候，这段恶意脚本就出现在了DOM里：
![]({{site.url}}{{site.baseurl}}/assets/img/no_subject/dom_script.png)

这样这段脚本就会被执行，然后可以进行窃取cookie之类的攻击。

#### 2.反射型XSS攻击

在反射型XXS攻击中，攻击者利用服务器响应信息包含请求信息来进行攻击。
还是举个例子：
我们搭建个简单的Node服务，把URL参数返回给页面。
```javascript
var express = require('express');
var router = express.Router();
router.get('/', function(req, res, next) { 
    res.render('index', { title: 'XSS Sample', xss:req.query.xss });
});
module.exports = router;
```
```html
<!DOCTYPE html>
<html>
<head>
  <title><%= title %></title>
</head>
<body>
  <h1><%= title %></h1>
  <div>
      <%- xss %>
  </div>
</body>
</html>
```

然后比如我们打开http://localhost:3000/?xss=helloworld这个链接，页面就会展示helloworld：
![]({{site.url}}{{site.baseurl}}/assets/img/no_subject/xss_sample.png)

但是如果把URL参数改成xss=\<script>console.log('这是恶意脚本')\</script>，这个脚本就会被执行：
![]({{site.url}}{{site.baseurl}}/assets/img/no_subject/script_invoke.png)

这就是反射型XSS攻击。
攻击者经常通过诱导用户点击恶意链接来实施攻击。

知道了如何发起攻击，下面我们就来看看如何有效防范这类攻击。

### 如何防范XSS攻击

#### 1.服务器转码和过滤

在服务器端对某些字符进行转码或者过滤，破坏恶意脚本的结构，已达到脚本在页面无法执行的目的。

#### 2.利用CSP策略


##### 1.限制加载其他域的资源；

##### 2.禁止向其他域提交数据；

##### 3.禁止执行内联脚本和未授权脚本；

##### 4.提供上报机制，方便尽快发现攻击。

关于CSP的更多内容，大家感兴趣可以自己查询了解。

#### 3.对cookie使用HttpOnly

服务器为响应头的`set-cookie`属性设置`HttpOnly`标记，这样这段cookie只能用于http请求，JavaScript是不能读取到的，这样就避免了攻击者窃取用户cookie。

好了，本文内容就到这里。

**原创文章，转载请注明出处！**