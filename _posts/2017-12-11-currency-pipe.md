---
layout: post
title: Angular：CurrencyPipe关于人民币符号￥的问题
summary: 在不熟悉的情况下使用CurrencyPipe很容易得到预期外的结果。
featured-img: emile-perron-190221
categories: Angular
---

做项目（Angular项目）时经常需要处理金额的显示，需要在金额前面加上￥，但又不想用简单在前面加“￥”这么不优雅的方式，于是想到了CurrencyPipe。毕竟，CurrencyPipe还可以格式化数字。
按照官方提供的[文档](https://www.angular.cn/api/common/CurrencyPipe)，我写出这样的代码：
```html
<span>{ {num | currency: 'CNY':'symbol-narrow'} }</span>
```
刷新页面，应该没什么问题！
什么，怎么是CN￥，不应该是￥吗？
于是我检查代码，以为把`symbol-narrow`写成了`symbol`，可是并没有，百思不得其解！
原来这是Angular的问题！在Angular 2.x - 4.x版本里（5.x已经可以自动识别），在使用货币管道处理人民币之前，应先对Angular进行一些本地化的工作。
如何使其本地化？
在你的app module中：
```typescript
import { LOCALE_ID } from '@angular/core';
@NgModule({
  providers: [
    { provide: LOCALE_ID, useValue: "ch-CN" }
  ]
})
```
这样设置之后，刷新页面，现在可以正确显示￥了！