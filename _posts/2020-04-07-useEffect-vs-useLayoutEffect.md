---
layout: post
title: useEffect和useLayoutEffect，有何不同
summary: 在React里面，有这么两个hook：`useEffect`和`useLayoutEffect`，他们的作用很相似。但他们并不是完全相同
featured-img: react
categories: React
---

在React里面，有这么两个hook：`useEffect`和`useLayoutEffect`，他们的作用很相似。

但他们并不是完全相同，本文通过实例的对比，让你清楚看到他们之间不一样的地方。

## useEffect和useLayoutEffect的不同之处

这一切的不同，都是在————时间上。

#### useEffect：

`useEffect`是异步的。换句话说，它不会阻塞其他的任务。

看起来就像这样：

1. 你以某种方式触发页面渲染（比如`setState`）
2. React渲染页面
3. 页面更新（视觉上）
4. 运行`useEffect`

#### useLayoutEffect

而`useLayoutEffect`是同步的，或者说，阻塞？React是执行完`useLayoutEffect`，然后才会去更新页面的。

看起来就像这样：

1. 你以某种方式触发页面渲染（比如`setState`）
2. React渲染页面
3. 运行`useLayoutEffect`，然后React等它执行完
4. 页面更新（视觉上）

## 什么时候用useEffect，什么时候用useLayoutEffect？

这么说吧，90%的情况都是使用`useEffect`。

这里有两个在线实例，去玩一下，相信你就会知道什么时候用哪个了：

- [使用useEffect的例子](https://codesandbox.io/s/useeffect-flash-on-render-gh6l7)
- [使用useLayoutEffect的例子](https://codesandbox.io/s/uselayouteffect-no-flash-tiuq1)

在页面上点击，看到区别了吗？

用`useEffect`，页面会闪一下，而`useLayoutEffect`却不会有这种情况。

以上就是本文全部内容了，感谢阅读！