---
# Documentation: https://sourcethemes.com/academic/docs/managing-content/

title: "What is RoI?"
subtitle: ""
summary: "本文解释RoI(Region of Interest)的概念"
authors: ["yuxinzhao"]
tags: ["RoI", "computer vision"]
categories: ["computer vision"]
date: 2019-07-15T11:07:26+08:00
lastmod: 2019-07-15T11:07:26+08:00
featured: false
draft: false

# Featured image
# To use, add an image named `featured.jpg/png` to your page's folder.
# Focal points: Smart, Center, TopLeft, Top, TopRight, Left, Right, BottomLeft, Bottom, BottomRight.
image:
  caption: "RoI Concept"
  focal_point: ""
  preview_only: false

# Projects (optional).
#   Associate this post with one or more of your projects.
#   Simply enter your project's folder or file name without extension.
#   E.g. `projects = ["internal-project"]` references `content/project/deep-learning/index.md`.
#   Otherwise, set `projects = []`.
projects: []
---





## RoI概念

RoI的全称是Region of Interest，中文名称是"感兴趣区域"。

RoI是从图像中选择的一个图像区域，这个区域是你进行图像分析的重点。圈出这块区域可以得到一个子图像(subimage)，之后就可以在这个区域内使用进行进一步处理。

**目的：**

+ 减少处理时间
+ 增加精度