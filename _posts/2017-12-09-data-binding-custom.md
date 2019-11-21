---
layout: post
title: Angular：如何实现自己的双向数据绑定
summary: 学过Angular的同学都知道，表单组件通过[(ngModel)]实现双向数据绑定，那我们能不能实现自己的双向数据绑定呢？答案是肯定的。
featured-img: angular
categories: Angular
---

学过Angular的同学都知道，表单组件通过`[(ngModel)]`实现双向数据绑定，那我们能不能实现自己的双向数据绑定呢？答案是肯定的。
Angular中，我们常常需要通过方括号`[]`和圆括号`()`实现组件间的交互：

![]({{site.url}}{{site.baseurl}}/assets/img/no_subject/data_binding_syntax.png)

那么在`[]`和`()`的基础上，如何实现组件的双向数据绑定？
例子如下。
子组件：
```html
<!--child.component.html-->

<h1>status in child: {{childStatus}}</h1>
<button (click)="toggle()">Toggle childStatus</button>
```
```typescript
//child.component.ts

export class ChildComponent implements OnInit{
  @Input() childStatus;
  @Output() childStatusChange = new EventEmitter();
  ngOnInit(){
    
  }
  toggle(){
        this.childStatus = !this.childStatus;
        this.childStatusChange.emit(this.childStatus);
  }
}
```
注意这里的写法，这是关键所在，输出属性必须在输入属性的基础上加上‘-Change’后缀。比如你的输入属性是`myData`，那么输出属性就必须是`myDataChange`。

父组件：
```html
<!--app.component.html-->

<test-binding [(childStatus)]="parentStatus"></test-binding>

<h1>status in parent: {{parentStatus}}</h1>
<button (click)="toggle()">Toggle parentStatus</button>
```
```typescript
//app.component.ts

import { Component,OnInit } from '@angular/core';
@Component({
  selector: 'my-app',
  templateUrl: './app.component.html',
  styleUrls: ['./app.component.css']
})
export class AppComponent implements OnInit{
  parentStatus: boolean = true;
  ngOnInit(){
    
  }
  toggle(){
    this.parentStatus = !this.parentStatus;
  }
}

```
在父组件我们初始化`parentStatus`为`true`，并把它传到子组件`ChildComponent`。
在子组件里，通过按钮我们可以改变childStatus的值，可以看到，子组件中的值改变的同时，父组件的值也跟着改变了。反过来，在父组件中通过按钮改变parentStatus的值，子组件中的值同样也跟着变化：

![]({{site.url}}{{site.baseurl}}/assets/img/no_subject/data_binding_sample.gif)


很简单对不对？！
你可以在[这里](https://stackblitz.com/edit/angular-uagcji?file=app%2Fapp.component.ts)查看和在线编辑本文代码，并实时查看效果！