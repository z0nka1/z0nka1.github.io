---
layout: post
title: Github Actions初体验
summary: 本文讲解如何利用Github Actions自动化构建和部署React应用到Github Pages
featured-img: cloud_moutain
---

这两天体验了一下Github去年推出的持续集成服务-Github Actions，以下是我的记录。

## 步骤

### 1. 申请Github Actions的使用权限。
Github Actions目前还处在beta阶段，要使用需要到[这里](https://github.com/features/actions/signup/)申请。申请通过后，你的仓库菜单会出现Actions一栏：

![]({{site.url}}{{site.baseurl}}/assets/img/no_subject/actions_menu.png)

### 2. 生成密钥并存储到项目。
你需要到这里去生成一个密钥：

![]({{site.url}}{{site.baseurl}}/assets/img/no_subject/github_secret.png)

然后，到项目的这里：

![]({{site.url}}{{site.baseurl}}/assets/img/no_subject/save_secret.png)

把刚才生成的密钥存起来。
**这里需要注意，密钥的名字跟生成时候填写的要一致。**

### 3. 配置工作流
然后，从这里进去，配置你的工作流：

![]({{site.url}}{{site.baseurl}}/assets/img/no_subject/config_workflow.png)

这里的配置字段非常多，具体可以查阅[官方文档](https://help.github.com/en/articles/workflow-syntax-for-github-actions)。

下面是我的配置：
```yaml
name: GitHub Actions Build and Deploy Demo
on:
  push:
    branches:
      - master
jobs:
  build-and-deploy:
    runs-on: ubuntu-latest
    steps:
    - name: Checkout
      uses: actions/checkout@master

    - name: Build and Deploy
      uses: JamesIves/github-pages-deploy-action@master
      env:
        ACCESS_TOKEN: ${{ secrets.token_for_react_ci }}
        BRANCH: gh-pages
        FOLDER: build
        BUILD_SCRIPT: npm install && npm run build
```
解释一下上面的配置：
1. name是这个workflow的名字。
2. on表示触发条件。这里是当push到master分支的时候触发。
3. 然后有一个job，运行在```ubuntu-latest```虚拟机环境。该job有两个step：第一个step使用的action是```actions/checkout@master```；第二个step使用的action是```JamesIves/github-pages-deploy-action@master```，这个step需要四个环境变量。**这里需要注意，```ACCESS_TOKEN```的密钥名字要跟你在步骤2存储到项目的一致**，这里我的密钥名字是```token_for_react_ci```。

### 4. 配置homepage
在你本地项目的```package.json```加一个字段：```homepage```，字段值会成为该项目的预览地址。比如我的是```https://z0nka1.github.io/github-actions-taste```，**记得要把用户名替换成你自己的Github用户名**。

然后把该项目推送到Github远程仓库，这时在项目的Actions菜单下可以看到正在运行刚才配置的workflow：

![]({{site.url}}{{site.baseurl}}/assets/img/no_subject/workflow.png)

等运行完毕，打开刚才配置的```homepage```：[https://z0nka1.github.io/github-actions-taste](https://z0nka1.github.io/github-actions-taste)，可以看到已经部署好了：

![]({{site.url}}{{site.baseurl}}/assets/img/no_subject/deploy_success.png)