---
layout: post
title: angular aot 编译报错 ERROR in ./src/main.ts 解决方法
summary: 非常常见的编译报错。
featured-img: emile-perron-190221
categories: Angular
---

昨天打包项目时遇到下图这样的错误：

![]({{site.url}}{{site.baseurl}}/assets/img/no_subject/aot_err.png)


开始以为了某些模块存在但未使用，折腾一番无果，后来升级angular-cli就搞定了，方法很简单：

1、删掉node_modules

2、更改package.json文件cli版本到最新稳定版

3、npm install 重新下载依赖

再次aot编译，搞定！