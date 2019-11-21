---
layout: post
title: Angular：自定义表单控件
summary: 分享一个最近写的Angular表单控件，欢迎交流讨论。
featured-img: angular
categories: Angular
---

分享一个最近写的支持表单验证的时间选择组件。
```typescript
import {AfterViewInit, Component, forwardRef, Input, OnInit, Renderer} from "@angular/core";
import {ControlValueAccessor, NG_VALUE_ACCESSOR} from "@angular/forms";
@Component({
    selector: 'time-picker',
    template: `
        <div id="container">
            <input id="input_box" readonly
                   [(ngModel)]="_value"
                   [ngStyle]="{'width.px': inputWidth, 'height.px': inputHeight}"
                   (click)="onInputClick($event)"
                   (focus)="onInputFocus($event)"
                   (blur)="onInputBlur()">
    
            <div id="panel" *ngIf="showBox">
                <div class="ui-g">
                    <div class="ui-g-4 title">小时</div>
                    <div class="ui-g-8 title">分钟</div>
                </div>
                <div class="ui-g">
                    <div class="ui-g-4">
                        <span class="item" *ngFor="let hour of hours" [ngClass]="{'selected': hour == selectedHour}" (click)="onHourClick(hour, $event)">{{hour}}</span>
                    </div>
                    <div class="ui-g-8">
                        <span class="item" *ngFor="let minute of minutes" [ngClass]="{'selected': minute == selectedMinute}" (click)="onMinuteClick(minute, $event)">{{minute}}</span>
                    </div>
                </div>
            </div>
        </div>
    `,
    styles: [`
        :host{
            display: inline-block;
        }
        #container{
            position: relative;
        }
        #input_box{
            outline: none;
            box-sizing: border-box;
            padding: 0 3px;
        }
        #panel{
            position: absolute;
            width: 400px;
            background-color: white;
            box-shadow: 0 2px 8px 4px rgba(0,0,0,0.2);
            z-index: 2000;
        }
        .title{
            text-align: center;
        }
        .item{
            display: inline-block;
            width: 30px;
            height: 30px;
            line-height: 30px;
            text-align: center;
            cursor: pointer;
        }
        .item:hover, .item.selected{
            background-color: #0b7dd8;
            color: white;
        }
    `],
    providers: [
        {
            provide: NG_VALUE_ACCESSOR,
            useExisting: forwardRef(() => TimePicker), // 此时TimePicker未声明，因此需要使用forwardRef
            multi: true
        }
    ]
})
export class TimePicker implements ControlValueAccessor, OnInit, AfterViewInit{ // ControlValueAccessor，一座表单控件和原生元素或自定义输入组件之间的桥梁
    @Input() inputWidth = 100; // 输入框宽度
    @Input() inputHeight = 30; // 输入框高度
    _value: string;
    showBox = false; // 控制选择面板显示与否，true为显示
    hours = []; // 小时数组
    minutes = []; // 分钟数组
    hourIsSelect = false; // 打开选择面板后，标记小时是否被点击
    minIsSelect = false; // 打开选择面板后，标记分钟是否被点击

    selectedHour; // 当前小时
    selectedMinute; // 当前分钟

    bodyClickListener: any;

    constructor(
        public renderer: Renderer
    ){}

    ngOnInit(){
        for(let i = 0; i < 24; i++){
            let h;
            if(i < 10){
                h = '0' + i;
            }else{
                h = '' + i;
            }
            this.hours.push(h);
        }
        for(let j = 0; j < 60; j++){
            let m;
            if(j < 10){
                m = '0' + j;
            }else{
                m = '' + j;
            }
            this.minutes.push(m);
        }
    }

    ngAfterViewInit(){
        this.bodyClickListener = this.renderer.listenGlobal('body','click', () => { this.hide() });
    }

    onChange = (time: string) => {};
    onTouched = () => {};

    get value(): string{
        return this._value;
    }

    set value(val: string){
        if(val !== this._value){
            this._value = val;
            this.onChange(val);
        }
    }

    /**
     * 实现ControlValueAccessor中的方法，用于将model显示到view
     * @param val
     */
    writeValue(val: string): void{
        if(val !== this._value){
            this._value = val;
        }
        if(this._value){
            let time = this._value.split(':');
            this.selectedHour = time[0];
            this.selectedMinute = time[1];
        }
    }

    /**
     * 实现ControlValueAccessor中的方法，用于通知Angular值已被修改
     * @param fn
     */
    registerOnChange(fn: (time: string) => void): void{
        this.onChange = fn;
    }

    /**
     * 实现ControlValueAccessor中的方法，用于通知Angular输入框被鼠标聚焦过
     * @param fn
     */
    registerOnTouched(fn: () => void): void{
        this.onTouched = fn;
    }

    /**
     * 隐藏选择面板
     */
    hide(){
        this.showBox = false;
        this.hourIsSelect = false;
        this.minIsSelect = false;
    }

    /**
     * 显示选择面板
     */
    show(){
        this.showBox = true;
    }

    /**
     * 输入框获得焦点
     * @param event
     */
    onInputFocus(event){
        event.stopPropagation();
        this.show();
    }

    /**
     * 输入框失去焦点
     */
    onInputBlur(){
        this.onTouched();
    }

    /**
     * 输入框点击
     * @param event
     */
    onInputClick(event){
        event.stopPropagation();
    }

    /**
     * 选择小时
     * @param h
     * @param event
     */
    onHourClick(h, event){
        event.stopPropagation();

        this.selectedHour = h;
        this.value = this.selectedHour + ':' + (this.selectedMinute ? this.selectedMinute : '00');

        this.hourIsSelect = true;
        if(this.hourIsSelect && this.minIsSelect){
            this.hide();
        }
    }

    /**
     * 选择分钟
     * @param min
     * @param event
     */
    onMinuteClick(min, event){
        event.stopPropagation();

        this.selectedMinute = min;
        this.value = (this.selectedHour ? this.selectedHour : '00') + ':' + this.selectedMinute;

        this.minIsSelect = true;
        if(this.hourIsSelect && this.minIsSelect){
            this.hide();
        }
    }
}
```