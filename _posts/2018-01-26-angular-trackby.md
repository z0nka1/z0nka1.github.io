---
layout: post
title: Angular：利用trackBy提升性能
summary: trackBy让列表渲染更高效。
featured-img: angular
categories: Angular
---

在Angular的模板中遍历一个集合（collection）的时候你会这样写：
```html
<ul>
  <li *ngFor="let item of collection">{{item.id}}</li>
</ul>
```
有时你会需要改变这个集合，比如从后端接口返回了新的数据。那么问题来了，Angular不知道怎么跟踪这个集合里面的项，不知道哪些该添加哪些该修改哪些该删除。结果就是，Angular会把该集合里的项全部移除然后重新添加。就像这样：
![]({{site.url}}{{site.baseurl}}/assets/img/no_subject/trackBy_1.gif)


这样做的弊端是会进行大量的DOM操作，而DOM操作是非常消耗性能的。
那么解决方案是，为*ngFor添加一个trackBy函数，告诉Angular该怎么跟踪集合的各项。trackBy函数需要两个参数，第一个是当前项的index，第二个是当前项，并返回一个唯一的标识，就像这样：
```typescript
import{ Component } from '@angular/core';

@Component({
  selector: 'trackBy-test',
  template: `
  <ul><li *ngFor="let item of items; trackBy: trackByIndex">{{item.name}}</li></ul>
  <button (click)="getItems()">Get Items</button>
  `
})
export class TrackByCmp{
  items: any[]=[];
  constructor(){
    this.items = [{name:'Tom'},{name:'Jerry'},{name:'Kitty'}];
  }
  getItems(){
    this.items = [{name:'Tom'},{name:'Jerry'},{name:'Mac'},{name:'John'}];
  }
  trackByIndex(index, item){
    return index;
  }
}
```
这样做之后，Angular就知道哪些项变动了：
![]({{site.url}}{{site.baseurl}}/assets/img/no_subject/trackBy_2.gif)


我们可以看到，DOM只重绘了修改和增加的项。而且，再次点击按钮是不会重绘的。但是在没有添加trackBy函数的时候，重复点击按钮还是会触发重绘的（可以回头看第一个GIF）。
以上是全部内容！