---
# Documentation: https://sourcethemes.com/academic/docs/managing-content/

title: "Windows PATH 长度限制最多2047个字符"
subtitle: ""
summary: "最近使用windows安装一些软件的过程中，出现了环境变量PATH最长限制2047个字符的问题。"
authors: ["yuxinzhao"]
tags: ["windows", "path"]
categories: ["system"]
date: 2019-07-20T09:53:59+08:00
lastmod: 2019-07-20T09:53:59+08:00
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

最近使用windows安装一些软件的过程中，出现了环境变量PATH最长限制2047个字符的问题。所幸我习惯下载软件的zip包，自己解压后再将其添加到环境变量中，因此才能及时发现这个问题。用安装包安装可能还不会提示这个问题，出情况也就比较难去定位问题了。

## Problem 问题

+ 环境变量PATH (或者是Path，windows的环境变量不区分大小写) 最大长度为2047个字符，要添加新的位置到环境变量中就超出了限制。

## Solution 解决方法

**用一个新的环境变量PATH2来拓展PATH**，具体步骤如下：

1. 打开 `计算机 >> 属性 >> 高级系统设置 >> 环境变量`
2. 在**系统变量**中找到 `PATH` (不区分大小写)， 将其名称改成 `PATH2`。
3. 新建一环境变量 `PATH`，在其中添加一个值`%PATH2%` 用于将 `PATH2` 包含进来。

完成。

由于新的 `PATH` 的只有 `%PATH2%` 一个值，后续要添加到环境变量中的位置都可以添加到 `PATH` 中，相当于扩容了 `PATH`，而 `PATH2` 是透明的，对以后的操作并不会有什么影响。

