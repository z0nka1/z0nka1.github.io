---
layout: post
title: Angular：实现一个彩虹输入框
summary: 通过实现一个彩虹输入框学习@HostBinding()和@HostListener()。
featured-img: emile-perron-190221
categories: Angular
---

@HostBinding()和@HostListener()在自定义指令时非常有用。@HostBinding()可以为指令的宿主元素添加类、样式、属性等，而@HostListener()可以监听宿主元素上的事件。

>@HostBinding()和@HostListener()不仅仅用在自定义指令，只是在自定义指令中用的较多

>本文基于Angular2+

下面我们通过实现一个在输入时实时改变字体和边框颜色的彩虹输入框，学习@HostBinding()和@HostListener()的用法。
```typescript
import { Directive, HostBinding, HostListener } from '@angular/core';

@Directive({
  selector: '[appRainbow]'①
})
export class RainbowDirective{
  possibleColors = [
    'darksalmon', 'hotpink', 'lightskyblue', 'goldenrod', 'peachpuff',
    'mediumspringgreen', 'cornflowerblue', 'blanchedalmond', 'lightslategrey'
  ];②
  @HostBinding('style.color') color: string;
  @HostBinding('style.borderColor') borderColor: string;③
  @HostListener('keydown') onKeydown(){④
    const colorPick = Math.floor(Math.random() * this.possibleColors.length);
    this.color = this.borderColor = this.possibleColors[colorPick];
  }
}
```
说一下上面代码的主要部分：
①：为我们的指令取名为appRainbow
②：定义我们需要展示的所有可能的颜色
③：定义并用@HostBinding()装饰color和borderColor，用于设置样式
④：用@HostListener()监听宿主元素的keydown事件，为color和borderColor随机分配颜色

OK，现在就来使用我们的指令：
```
<input appRainbow>
```
效果就像这样：
![]({{site.url}}{{site.baseurl}}/assets/img/no_subject/rainbow_input.gif)


>NOTE：别忘了把指令引入你的模块

以上！