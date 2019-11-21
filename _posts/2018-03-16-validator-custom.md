---
layout: post
title: Angular：Reactive Form的使用方法和自定义验证器
summary: 介绍Reactive Form的使用方法，然后实现一个自定义表单验证器。
featured-img: angular
categories: Angular
---

本文将介绍Angular（Angular2+）中Reactive Form的有关内容，包括：
* Reactive Form创建方法
* 如何使用验证
* 自定义验证器

下面开始进入正文！

##Reactive Form创建方法
首先我们需要使用FormBuilder创建一个FormGroup，就像这样：
```typescript
registerForm: FormGroup;
constructor(
    private fb: FormBuilder,
) {}
ngOnInit() {
  this.registerForm = this.fb.group({
    firstName: [''],
    lastName: [''],
  })
}
```
上面的`firstName`和`lastName`是由你自己取的，分别表示一个表单控件名称。他们的值都是一个数组，数组第一个参数表示初始值。如果你不给它赋予初始值，则给它一个空字符串：''。
然后HTML页面就像这样：
```html
<form [formGroup]="registerForm" (ngSubmit)="handleSubmit(registerForm)">
  <label>FirstName:</label>
  <input formControlName="firstName">
  <label>LastName:</label>
  <input formControlName="lastName">
  <button type="submit">Submit</button>
</form>
```
这样就创建了一个非常简单的表单！

##如何使用验证
表单的输入项经常是要进行验证的，那该怎么验证呢？
其实很简单，Angular已经为我们写好了一些常用的验证器，就像这样使用就可以了：
```typescript
ngOnInit() {
  this.registerForm = this.fb.group({
    firstName: ['', Validators.required],
    lastName: ['', Validators.pattern('[A-Za-z0-9]*')],
  })
}
```
可以使用“必要”验证器，也可以使用正则，你只需传入一个正则表达式就可以了。除此之外，还有`minLength`和`maxLength`等。
如果一个控件需要多个验证器，可以把它们放在一个数组里面：
```typescript
lastName: ['', [Validators.pattern('[A-Za-z0-9]*'), Validators.required]]
```
如果你希望在输入时得到一些提示，可以这样写你的HTML：
```html
<form [formGroup]="registerForm" (ngSubmit)="handleSubmit(registerForm)">
  <label>FirstName:</label>
  <input formControlName="firstName">
  <p *ngIf="registerForm.controls.firstName.touched && registerForm.controls.firstName.invalid">
    This field is required!
  </p>
  <label>LastName:</label>
  <input formControlName="lastName">
  <p *ngIf="registerForm.controls.lastName.hasError('pattern')">
    Invalid input!
  </p>
  <button type="submit" [disabled]="!registerForm.valid">Submit</button>
</form>
```
第一个控件，在你“touch”过又为空的情况下会给出提示；第二个控件，在输入不符合正则表达式规则的情况下给出提示，`hasError`方法还可以传入`required`、`minLength`等参数，分别对应不同的验证器；最后，在输入还未符合规则的时候，Submit按钮是处于禁用状态的。

Angular提供的几个验证器在实际项目中往往是不够用的，因此我们需要自定义验证器，以满足我们的业务需求！

##自定义验证器
验证器其实就是一个有着`return`的方法！
现在我们来写一个验证器，验证一个网址输入框的值是否符合规则：
```typescript
export function validateUrl(control: AbstractControl){
  if(control.value){
    if(!control.value.startsWith('www') || !control.value.includes('.io')){
      return {
        inValidUrl: true
      }
    }
  }
  return null;
}
```
我们测试输入值是否以“www”开头，并且包含“.io”。然后使用方法跟Angular提供的验证器一样：
```typescript
this.registerForm = this.fb.group({
    firstName: ['', Validators.required],
    lastName: ['', [Validators.pattern('[A-Za-z0-9]*'), Validators.required]],
    website: ['', validateUrl], // <---
})
```
然后，如果你希望在输入不符合validateUrl验证器规则时得到提示，你可以这样写你的HTML：
```html
<label>Website:</label>
  <input formControlName="website">
    <p *ngIf="registerForm.controls.website.hasError('inValidUrl')">  //<---
    Url must starts with www and includes .io
  </p>
```
hasError方法的参数就是validateUrl验证器返回对象的key。
OK，今天的内容就到这里。