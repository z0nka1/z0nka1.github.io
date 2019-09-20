---
layout: post
title: Angular：利用内容投射向组件输入遍历模板
summary: 不用*ngIf，也能根据需要订制组件不同外观。
featured-img: emile-perron-190221
categories: Angular
---

现在，我们写一个组件puppiesListCmp，用于显示小狗狗的列表：
```typescript
//puppies-list.component.ts
@Component({
  selector: 'puppies-list',
  template: `
    <div *ngFor="let puppy of puppies">
      <span>{{puppy.name}}</span>
      <span>{{puppy.age}}</span>
      <span>{{puppy.color}}</span>
    </div>
`
})
export class puppiesListCmp{
  @Input() puppies: Puppy[];
}
interface Puppy {
  name: string,
  age: number,
  color: string
}
```
然后这样使用：
```typescript
//app.component.ts
@Component({
  selector: 'my-app',
  template: `
    <puppies-list [puppies]="puppies"></puppies-list>
`
})
export class App{
  puppies = [
    {
      name: "sam",
      age: 0.6,
      color: "yellow"
    },
    {
      name: "bingo",
      age: 1.5,
      color: "black"
    }
  ]
}
```
效果就像这样：

![]({{site.url}}{{site.baseurl}}/assets/img/no_subject/content_projection_1.png)

但是，我希望我们的puppiesListCmp组件可以满足不同的需求，比如在数据不变的情况下只显示小狗狗的name和color，就像这样：

![]({{site.url}}{{site.baseurl}}/assets/img/no_subject/content_projection_2.png)

这就是本文的重点了。我们需要实现用户自定义模板！
现在我们不写死组件的模板了，而是让用户从外部输入!
首先，我们的组件模板：
```html
<div *ngFor="let puppy of puppies">
      <span>{{puppy.name}}</span>
      <span>{{puppy.age}}</span>
      <span>{{puppy.color}}</span>
</div>
```
等价于：
```html
<ng-template ngFor let-puppy [ngForOf]="puppies">
      <div>
        <span>{{puppy.name}}</span>
        <span>{{puppy.age}}</span>
        <span>{{puppy.color}}</span>
      </div>
</ng-template>
```
然后，用@ContentChild（关于@ContentChild可以查看[这里](https://blog.angular-university.io/angular-ng-content/)，需翻墙）获取到外部（相对puppiesListCmp组件而言）自定义模板，并赋给ngForTemplate。也就是说，这部分：
```html
<div>
    <span>{{puppy.name}}</span>
    <span>{{puppy.age}}</span>
    <span>{{puppy.color}}</span>
</div>
```
不再像之前那样写死在组件里了，而是由使用者在父组件中自定义，然后利用Angular的内容投射（Content Projection），投射到puppiesListCmp组件里面。就像这样：
```typescript
//puppies-list.component.ts
import { Component, Input, ContentChild, TemplateRef } from '@angular/core';
import { NgForOfContext } from '@angular/common';
@Component({
  selector: 'puppies-list',
  template: `
<ng-template ngFor let-puppy [ngForOf]="puppies" [ngForTemplate]="tpl"></ng-template>
`
})
export class puppiesListCmp{
  @Input() puppies: Puppy[];
  @ContentChild(TemplateRef) tpl: TemplateRef<NgForOfContext<Puppy>>
}
interface Puppy {
  name: string,
  age: number,
  color: string
}
```
这样我们的组件就算完成了。然后我们使用它：
```typescript
//app.component.ts
@Component({
  selector: 'my-app',
  template: `
<puppies-list [puppies]="puppies">
  <ng-template let-puppy>
    <div>
      <span>{{puppy.name}}</span>
      <span>{{puppy.age}}</span>
      <span>{{puppy.color}}</span>
    </div>
  </ng-template>
</puppies-list>
`
})
```
效果还是一样的：

![]({{site.url}}{{site.baseurl}}/assets/img/no_subject/content_projection_1.png)

如果我们只要显示小狗狗的name和color，只要这样写就好了：
```typescript
//app.component.ts
@Component({
  selector: 'my-app',
  template: `
<puppies-list [puppies]="puppies">
  <ng-template let-puppy>
    <div>
      <span>{{puppy.name}}</span>
      <span>{{puppy.color}}</span>
    </div>
  </ng-template>
</puppies-list>
`
})
```
效果就像这样：

![]({{site.url}}{{site.baseurl}}/assets/img/no_subject/content_projection_2.png)

这样的组件很灵活，想要什么样的效果都可以定制!
好了，本文就到此为止了。