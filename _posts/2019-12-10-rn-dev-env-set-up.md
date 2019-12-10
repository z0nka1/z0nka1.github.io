---
layout: post
title: React Native：搭建开发环境
summary: RN开发环境搭建采坑实录
featured-img: code
categories: React Native
---

## 写在开头

**RN开发环境搭建非常繁琐，你会遇到很多的报错，做好心理准备！**

**每个人的情况都不一样，遇到的报错也不尽相同，这里只能提供部分情况借鉴！**

**这里讲的是在 Windows 平台下搭建 Android 开发环境**

下面分几个步骤，讲述我是怎么搭建的。

## 第一步：下载Node

这个没什么好说的，因为我是前端开发，所以本来就有Node环境，只是注意一点，**Node版本必须 >= 10。**

## 第二步：下载安装Python

官网的话下载速度非常慢，建议你直接用搜索引擎搜索下载，**必须是2.x版本。**

安装完成之后，一定要记得配置环境变量，把Python安装根目录加到Path的系统变量（注意不是用户变量）里面去。

然后**新开一个**命令行窗口运行`python`，如果进入交互模式，说明配置成功了。

## 第三步：下载安装JDK

版本必须是1.8。

同样的，安装完成之后要配置环境变量，首先在系统变量下面点击新建，变量名填入`JAVA_HOME`，变量值填你的JDK安装根目录，点击确定。

然后，编辑Path变量，分别新增`%JAVA_HOME%\bin`和`%JAVA_HOME%\jre\bin`进去，注意这里是新增两条，不是写在一起。

然后**新开一个**命令行窗口运行`javac`，如果输出用法之类的信息，就是配置成功了。

## 第四步：下载 react-native-cli

命令行运行`npm install -g react-native-cli`。

## 第五步：下载安装 Android Studio

下载链接：https://developer.android.google.cn/studio#downloads

这是谷歌专门为中国开发者搭建的，速度会快一些。

**从这里开始，一定要翻墙，一定要翻墙，一定要翻墙，而且越稳定越好！否则很多依赖下载不了。**

后面的步骤参考[这里](https://reactnative.cn/docs/getting-started/)，我是一步步照着来的。

**新建模拟器的时候，注意有Google Play 和Google APIs，我们选择Google APIs，因为我开始选了Google Play，后面启动的时候明明模拟器的开着的，依然会报 No online devices found 这种错误。**

## 第六步：初始化项目并启动

运行`react-native init appName`初始化一个RN项目。

然后打开你的Android Studio，并且打开模拟器。

进入项目的根目录：`cd appName`。

启动项目：`react-native run-android`。

这个过程会持续十几分钟到几十分钟不等，会下载相当多的依赖，一定要确保翻墙成功，不然很多依赖会下载失败。

如果遇到报错，看看错误信息，如果包含Cannot get [网址]或者timeout之类的字段，99%是依赖下载失败了，这时继续运行`react-native run-android`，继续下载依赖。只要是依赖下载失败就不断运行`react-native run-android`，直到全部依赖下载成功为止。

这个过程要保持耐心，因为报错会源源不断。

根据报错信息，大概可以知道是什么问题，针对问题去解决，利用好搜索引擎和Stack Overflow之类的社区，不会很难！

OK，本文到这里就要结束了，感谢阅读！