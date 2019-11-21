---
layout: post
title: Angular：OnPush变化检测策略介绍
summary: 在OnPush策略下，Angular不会运行变化检测（Change Detection ），除非组件的`input`接收到了“新值”。
featured-img: angular
categories: Angular
---

在OnPush策略下，Angular不会运行变化检测（Change Detection ），除非组件的`input`接收到了“新值”。接收到新值的意思是，`input`的值或者引用发生了变化。这样听起来不好理解，看例子：
子组件接收一个`balls`（别想歪:)）输入，然后在模板遍历这个`balls`数组并展示出来。初始化2秒后，往`balls`数组push一个`new ball`：
```typescript
//balls-list.component.ts
@Component({
  selector: 'balls-list',
  template: `
    <div *ngFor="let ball of balls">{{ball}}</div>
  `
})
export class BallsList implements OnInit{
  @Input() balls: any[];
  ngOnInit(){
    setTimeout(() => {
      this.balls.push('new ball');
    },2000)
  }
}
```
**注意：因为鼠标键盘事件会触发变化检测，所以这里使用定时器！**
现在是不使用OnPush策略的情况。2秒后，new ball就被添加到视图中了。
当使用OnPush变化检测策略的时候，new ball并不会被添加到视图中：
```typescript
//balls-list.component.ts
@Component({
  selector: 'balls-list',
  template: `
    <div *ngFor="let ball of balls">{{ball}}</div>
  `,
  changeDetection: ChangeDetectionStrategy.OnPush
})
export class BallsList implements OnInit{
  @Input() balls: any[];
  ngOnInit(){
    setTimeout(() => {
      this.balls.push('new ball');
    },2000)
  }
}
```
但是，如果在父组件改变`input`的值，视图是会更新的，因为这样Angular会运行变化检测：
```typescript
//app.component.ts
@Component({
  selector: 'my-app',
  template: `
    <balls-list [balls]="balls"></balls-list>
    <button (click)="changeRef()">Change Ref</button>
  `,
})
export class AppComponent implements OnInit{
  balls: any[] = ['basketball','football'];
  ngOnInit(){}
  changeRef(){
    this.balls = ['baseball','pingpong'];
  }
}
```
这样就相当于改变了`balls`的引用。
但是，如果不改变`balls`的引用，只给它push一个值，视图是不会更新的：
```typescript
//app.component.ts
@Component({
  selector: 'my-app',
  template: `
    <balls-list [balls]="balls"></balls-list>
    <input #input>
    <button (click)="addBall(input.value)">Add a ball</button>
  `,
})
export class AppComponent implements OnInit{
  balls: any[] = ['basketball','football'];
  ngOnInit(){}
  addBall(ball){
    this.balls.push(ball);
  }
}
```
这时如果有需要，我们可以用`ChangeDetectorRef.detectChanges()`手动触发变化检测。
这样解释应该很清楚了！
>提示：OnPush配合Immutable.js使用效果更佳！

本文代码和演示可以在[这里](https://stackblitz.com/edit/angular-zkb9qk?file=app%2Ftest.component.ts)查看（建议电脑访问，自备梯子）。
不当之处欢迎指出，欢迎交流！