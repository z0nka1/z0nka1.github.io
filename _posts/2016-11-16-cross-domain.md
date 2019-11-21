---
layout: post
title: 跨域及其解决方案
summary: 如果两个URL，协议相同，域名相同，端口号相同，那么这两个URL就属于同域。那么外域就是，协议不同，或者域名不同，或者端口号不同。注意，这三者只要其中一个不同，就不属于同域。
featured-img: javascript
---

首先来明确一下“跨域”这个概念。
跨域指的是，到外域去取数据。那什么是“外域”呢？我们先来了解同域。同域指的是，同协议、同域名、同端口。如果两个URL，协议相同，域名相同，端口号相同，那么这两个URL就属于同域。那么外域就是，协议不同，或者域名不同，或者端口号不同。注意，这三者只要其中一个不同，就不属于同域。
我们看看例子：
```
http://www.jianshu.com/a.html
http://www.jianshu.com/b.html
//同域。协议相同，域名相同，端口号相同（默认80端口）
```

```
http://a.jianshu.com
http://b.jianshu.com
//不同域。域名不同
```

```
https://www.jianshu.com
http://www.jianshu.com
//不同域。协议不同
```

举例完毕，接下来将会讲讲以下三种跨域的方式。
1. CORS
2. 降域
3. JSONP

##CORS
CORS全称是跨域资源共享（Cross-Origin Resource Sharing），是一种Ajax跨域请求资源的方式。实现方式很简单，当你使用XMLHttpRequest发送请求时，如果该请求不符合同源策略，浏览器会给该请求加一个请求头：Origin，而后台会在返回结果中加一个响应头：Access-Control-Allow-Origin,浏览器判断该响应头是否包含Origin的值，如果包含，浏览器就会处理响应，我们就可以拿到数据；如果不包含，浏览器就会直接驳回，我们就拿不到数据。
那么怎么做才能让我们跨域拿到数据呢？很简单，我们只需在远程文件中加上这一行代码：
```
header("Access-Control-Allow-Origin","允许请求的URL，*为允许全部")
```
比如，` header("Access-Control-Allow-Origin","http://www.jianshu.com")`会允许来自 http://www.jianshu.com 的请求，而` header("Access-Control-Allow-Origin","*")`会允来自任何域的请求。

##降域
如果我们从 a.jianshu.com 请求 b.jianshu.com 的数据，像这种子页面不相同的情况就是适合利用降域来跨域了。实现起来也很简单，只需在本地文件和远程文件都加上这句代码：
```
document.domain="jianshu.com"
```
也就是说，直接把主网页的域名写上就可以了,这样就实现了利用降域来跨域。

##JSONP
好了，重点来了，JSONP是非常常用的跨域方法，它通过动态创建script标签来实现跨域。
众所周知，通过script来加载外部文件是不存在同源策略的限制的，我们可以请求任何域的文件而不需要跨域。确切的说，任何含有src或者href属性的标签都不存在同源策略的限制。利用这个特点，我们把远程文件的URL放到script标签的src中，这样就得到了远程文件中的数据，然后把这些数据作为参数传入一个函数，就可以按自己需求处理和呈现了。我们看看具体怎么实现：
```javascript
//远程数据地址
var url="http://api.jianshu.com/getSong.php?callback=handler";
//创建script标签
var script=document.createElement('script');
//把script标签src设为远程数据地址
script.setAttribute('src',url);
//把script标签加到head中
document.getElementsByTagName('head')[0].appendChild(script);
//回调处理函数
function handler(data){
	//some code here... 
}
```
**注意，要在远程数据地址尾部加上回调函数名， 服务器会动态用这个函数名包裹住数据，也就是将数据作为这个函数的参数，这样返回到本地之后就可以执行相应函数了。**
举个栗子，当我们以callback为getCity请求数据，服务器返回了的数据是这样的：
```javascript
getCity(
{"city":
[
	{	
        "name":"广州",
	"cid":"578"
	},
        {
        "name":"厦门",
        "cid":"586"
        }
]
}
)
```
我们的getCity函数是这样的：
```javascript
function getCity(data){
    console.log(data.city[0].name);
}
```
这样我们就log出了广州，跨域成功！是不是很简单啊！
好了，跨域就讲这么多。欢迎交流，欢迎指正！
原创文章，转载请注明出处！