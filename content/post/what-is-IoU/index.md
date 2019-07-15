---
# Documentation: https://sourcethemes.com/academic/docs/managing-content/

title: "What is IoU?"
subtitle: ""
summary: ""
authors: ["yuxinzhao"]
tags: ["IoU", "computer vision"]
categories: ["computer vision"]
date: 2019-07-15T20:20:32+08:00
lastmod: 2019-07-15T20:20:32+08:00
featured: false
draft: false

# Featured image
# To use, add an image named `featured.jpg/png` to your page's folder.
# Focal points: Smart, Center, TopLeft, Top, TopRight, Left, Right, BottomLeft, Bottom, BottomRight.
image:
  caption: "IoU"
  focal_point: ""
  preview_only: false

# Projects (optional).
#   Associate this post with one or more of your projects.
#   Simply enter your project's folder or file name without extension.
#   E.g. `projects = ["internal-project"]` references `content/project/deep-learning/index.md`.
#   Otherwise, set `projects = []`.
projects: []
---

在目标检测任务中常常用IoU来计算预测窗口与真实窗口的交叠率。本文介绍IoU的概念。

IoU (Intersection over Union, 交并比) 是两个窗口的交集与并集面积之比。

+ 区域 Region-1 与 Region-2 的交集如上图黄色区域
+ 区域 Region-1 与 Region-2 的并集如上图绿色区域

**IoU计算公式**:
$$
IoU = \frac{\text{Region-1} \cap \text{Region-2}}{\text{Region-1} \cup \text{Region-2}}
$$
