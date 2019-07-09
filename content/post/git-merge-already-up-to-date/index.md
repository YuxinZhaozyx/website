---
# Documentation: https://sourcethemes.com/academic/docs/managing-content/

title: "git merge命令提示Already up to date"
subtitle: ""
summary: "`master`分支没有变化导致的分支无法合并错误"
authors: ["yuxinzhao"]
tags: ["git"]
categories: ["git"]
date: 2019-07-09T14:45:33+08:00
lastmod: 2019-07-09T14:45:33+08:00
featured: false
draft: false

# Featured image
# To use, add an image named `featured.jpg/png` to your page's folder.
# Focal points: Smart, Center, TopLeft, Top, TopRight, Left, Right, BottomLeft, Bottom, BottomRight.
image:
  caption: ""
  focal_point: ""
  preview_only: false

# Projects (optional).
#   Associate this post with one or more of your projects.
#   Simply enter your project's folder or file name without extension.
#   E.g. `projects = ["internal-project"]` references `content/project/deep-learning/index.md`.
#   Otherwise, set `projects = []`.
projects: []
---



假定我有两个分支`master`和`dev`。当前处在`dev`分支。

```shell
$ git merge master
Already up to date.
```



## Cause of Errors 错误原因

`Already up to date`意味着已经是最新的了，即`dev`是在`master`的基础上修改的，而`master`自分出`dev`分支后就没修改过，因此`dev`分支是最新的，不需要于`master`合并，因为合并完的仓库和当前的`dev`分支是一模一样的。

其git分支图如下：

```shell
				 E -- F -- G(dev)
				/
A -- B -- C -- D(master)
```

其等同于:

```shell
A -- B -- C -- D(master) -- E -- F -- G(dev)
```

我们想要做的合并实际上是将`master`指向`dev`而已。

## Solution 解决方法

多提交一个空的`commit`，使两条分支叉开，再合并。

```shell
				 E -- F -- G(dev)
				/
A -- B -- C -- D -- H(master)
```

```shell
				 E -- F -- G(dev)
				/           \
A -- B -- C -- D -- H ------ I(master)
```

上图中`H`是一个不做任何修改的提交，其作用是将`master`和`dev`分到两个叉开的分支上，使得`master`不再是`dev`的父节点。

**具体命令:**

```sh
# 移动到master分支下
$ git checkout master
# 在master提交一个空内容
$ git commit --allow-empty -m "ready for merging"
# 合并master和dev分支
$ git merge dev
# 移除dev指针(可选)
$ git branch --delete dev
```

