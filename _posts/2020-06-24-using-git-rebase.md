---
layout: post
title: 使用git rebase合并提交记录
summary: 本文将通过`rebase`的两个常见使用场景，介绍`rebase`如何运用到实际中，以及解决问题的具体步骤。
featured-img: javascript
categories: javascript
---



`rebase`，日常开发经常需要用到的命令，用好它，可以让很多事情变得简单。

对于`rebase`理论部分的学习，我想官方文档已经写得足够好了，我无意班门弄斧。

但是对于实际应用，官方文档却少有提及。因此，本文将通过`rebase`的两个常见使用场景，介绍`rebase`如何运用到实际中，以及解决问题的具体步骤。

让我们开始吧！

## 场景一

假设你在`develop`分支进行开发，当你提交了一些代码之后，试图推送到远程`develop`分支：

```
git push origin develop
```

但是推送没有成功，并给出了这样的提示：

![]({{site.url}}{{site.baseurl}}/assets/img/no_subject/push_reject.png)

说明已经有人先于我们推送了代码，我们需要先拉取下来然后再推上去：

```
git pull
```

这时候，使用`git log`看看提交记录，就会发现多了一条：

![]({{site.url}}{{site.baseurl}}/assets/img/no_subject/merge_commit_msg.png)

多出来的这一条提交记录，如果我们不管它，当然也没什么问题。但是，我们希望把它去掉，因为实在不好看，而且没什么用。

这就是`rebase`可以派上用场的地方了！

命令行输入`git rebase`，回车。完成之后，再来看看现在的提交记录是什么样的：

![]({{site.url}}{{site.baseurl}}/assets/img/no_subject/after_rebase.png)

刚才多出来的那条提交信息不见了！

这时我们再推送到远程，提交记录变得简洁而线性！


## 场景二

假设我们在`feat-awesome`分支上面开发一个新功能，在开发这个功能的过程中，我们进行了多次提交。

当这个功能开发完成，我们把它合并到`develop`分支进行测试，这时，在`develop`分支，我们不希望看到一系列的提交记录，因为也有其他的特性分支合并到`develop`分支，这看起来很乱。

我们想把在`feat-awesome`分支开发的那个新功能的提交记录合并成一个，这样当合并到`develop`分支的时候就比较清晰简洁了。

我们使用`rebase`来达到这个目的（实际上，这是场景一的一种变体）。

首先，使用`git log`来打印出提交记录。

![]({{site.url}}{{site.baseurl}}/assets/img/no_subject/long_commit_log.png)

通过提交记录我们发现，新功能的开发是从`f707cb`这个提交开始的，所以，我们从它前一个提交开始操作：

```
git rebase -i 5e0208
```

（↑ i，interactive，进入交互模式的意思）

输入这个命令，回车之后就进入了vi交互界面：

![]({{site.url}}{{site.baseurl}}/assets/img/no_subject/vi_ui_01.png)

界面的上半部分列出了`5e0208`提交之后的所有提交记录，可以看到，每个提交记录前面都有个`pick`。我们先来解释一下这些关键字都是什么含义。

`pick`，或者`p`，表示这条提交是要保留的
`squash`，或者`s`，表示这条提交是要合并到上一条标示为`pick`的提交里面去

这里只要了解这两个关键字就可以了。

这里，我们只要保留最上面一条提交记录，所以把其他的`pick`都改为`squash`（或者`s`）。

但是，现在界面还处在不可编辑状态，我们需要按下键盘的`c`键进入可编辑状态。

编辑好后是这样的：

![]({{site.url}}{{site.baseurl}}/assets/img/no_subject/vi_ui_02.png)

然后按下`esc`键退出编辑状态，接下来输入`:wq`后回车进行保存并退出。

接下来又进入另一个交互界面，在这个交互界面，你可以编辑提交信息，然后依然按下`:wq`进行保存退出。

好了，提交记录合并操作完成了，我们来看看现在的提交记录是怎样的：

![]({{site.url}}{{site.baseurl}}/assets/img/no_subject/combine_commit.png)

可以看到，新功能开发的提交记录都合并成了一个。此时再推送到`develop`分支，`develop`的提交记录就变得既清晰又简洁了！