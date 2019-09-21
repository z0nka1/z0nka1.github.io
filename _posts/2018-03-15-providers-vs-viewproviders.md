---
layout: post
title: Angular：ViewProviders和Providers的区别
summary: 在Angular中使用依赖注入（DI）的时候，我们一般会使用providers。其实要做同样的事我们还有另外一个选择：viewProviders。
featured-img: river-bridge
categories: Angular
---

在Angular中使用依赖注入（DI）的时候，我们一般会使用`providers`。其实要做同样的事我们还有另外一个选择：`viewProviders`。
`viewProviders`允许我们定义只对组件的view可见的provider。下面我们用例子详细的说明这一点。
假设我们有一个简单的服务：
```typescript
// myService.service.ts
import { Injectable } from '@angular/core';

@Injectable()
export class MyService{
  testIfGetService(where){
    console.log('Got My Service in ' + where);
  }
}
```
这个服务很简单，只需要打印出在哪里调用了该服务。
然后有一个子组件，是用来投射到父组件里面的（等会将会看到）：
```typescript
// child.component.ts
import { Component } from '@angular/core';
import { MyService } from './myService.service';

@Component({
  selector: 'vp-child',
  template: `
    <div>This is child!!!</div>
  `
})
export class VPChild{
  constructor(
    private service: MyService
  ){
    this.service.testIfGetService('child');
  }
}
```
这个组件注入了`MyService`服务，调用`MyService`的`testIfGetService`方法，并传入`child`表明这是在child组件调用的。
还有另外一个子组件，这个组件是用来放在父组件的模板（template）里面的：
```typescript
// viewChild.component.ts
import { Component } from '@angular/core';
import { MyService } from './myService.service';

@Component({
  selector: 'vp-viewchild',
  template: `
    <div>This is viewChild!!!</div>
  `
})
export class ViewVPChild{
  constructor(
    private service: MyService
  ){
    this.service.testIfGetService('viewChild');
  }
}
```
这里同样注入`MyService`服务，调用`MyService`服务的`testIfGetService`方法，并传入`viewChild`。
最后是父组件：
```typescript
// parent.component.ts
import { Component } from '@angular/core';
import { MyService } from './myService.service';
@Component({
  selector: 'vp-parent',
  template: `
    <div>This is parent!!!</div>
    <ng-content></ng-content>
    <vp-viewchild></vp-viewchild>
  `,
  providers: [MyService]
})
export class VPParent{
  constructor(
    private service: MyService
  ){
    this.service.testIfGetService('parent');
  }
}
```
在父组件，用`providers`注册`MyService`，然后调用`MyService`的`testIfGetService`传入`parent`。
然后就像这样使用父组件：
```html
<vp-parent>
  <vp-child></vp-child>
</vp-parent>
```
运行程序，控制台打印出了结果：

![]({{site.url}}{{site.baseurl}}/assets/img/no_subject/provider_1.png)

一切就像预期那样！！
然后，我们用`viewProviders`代替`providers`注册`MyService`，看看会发生什么：
```typescript
// parent.component.ts
import { Component } from '@angular/core';
import { MyService } from './myService.service';
@Component({
  selector: 'vp-parent',
  template: `
    <div>This is parent!!!</div>
    <ng-content></ng-content>
    <vp-viewchild></vp-viewchild>
  `,
  viewProviders: [MyService] // <---
})
export class VPParent{
  constructor(
    private service: MyService
  ){
    this.service.testIfGetService('parent');
  }
}
```
这样修改之后，运行程序，发现报错了：

![]({{site.url}}{{site.baseurl}}/assets/img/no_subject/provider_2.png)

如果把contentChild注释掉，就像这样：
```html
<vp-parent>
  <!-- <vp-child></vp-child> -->
</vp-parent>
```
是不会报错的：

![]({{site.url}}{{site.baseurl}}/assets/img/no_subject/provider_3.png)

这就说明，在父组件用`viewProviders`注册的provider，对contentChildren是不可见的。而使用`providers`注册的provider，对viewChildren和contentChildren都可见！
补充说明：组件会逐级向上寻找provider，直到找到为止，否则就会抛出错误。就像这里：
```html
<vp-parent>
  <vp-child></vp-child>
</vp-parent>
```
`vp-child`往上找`MyService`的provider，结果在`vp-parent`找到了。但是在用`viewProviders`的时候，`vp-child`往上找，也就是到`vp-parent`，结果没找到，然后又去找`vp-parent`的父级，还是没找到（因为在这个例子里，我们只在`vp-parent`注册了`MyService`），然后又继续往上找……如此找到边界也没找到，所以抛出了一个错误。如果你不希望这样，可以使用`@Host`做出限制，就像这样：
```typescript
constructor(
    @Host() private service: MyService
){}
```
关于@Host()本文不作展开，有兴趣可以自行google。