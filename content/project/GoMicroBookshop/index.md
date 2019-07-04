---
# Documentation: https://sourcethemes.com/academic/docs/managing-content/

title: "GoMicroBookshop"
summary: "A bookshop project for my go-micro and micro service learning"
authors: ["yuxinzhao"]
tags: ["golang", "micro", "go-micro", "micro service"]
categories: ["project"]
date: 2019-07-04T12:27:03+08:00

# Optional external URL for project (replaces project detail page).
external_link: ""

# Featured image
# To use, add an image named `featured.jpg/png` to your page's folder.
# Focal points: Smart, Center, TopLeft, Top, TopRight, Left, Right, BottomLeft, Bottom, BottomRight.
image:
  caption: "Abstract Structure"
  focal_point: "Top"
  preview_only: false

# Custom links (optional).
#   Uncomment and edit lines below to show custom links.
# links:
# - name: Follow
#   url: https://twitter.com
#   icon_pack: fab
#   icon: twitter

url_code: "https://github.com/YuxinZhaozyx/GoMicroBookshop"
url_pdf: ""
url_slides: ""
url_video: ""

# Slides (optional).
#   Associate this project with Markdown slides.
#   Simply enter your slide deck's filename without extension.
#   E.g. `slides = "example-slides"` references `content/slides/example-slides.md`.
#   Otherwise, set `slides = ""`.
slides: ""
---

A bookshop project for my go-micro and micro service learning.

本项目依据项目 [microservice-in-cn](https://github.com/micro-in-cn/tutorials/tree/master/microservice-in-micro) 学习[micro](https://github.com/micro/micro)工具链以及微服务。



# 项目简介

 本项目为一个网上书店。



## 业务模块

- **用户**，users
- **库存**，inventory
- **权限**，auth
- **订单**，orders
- **支付**，payment



## 服务抽象结构

[![img](image/design.png)](https://github.com/YuxinZhaozyx/GoMicroBookshop/blob/master/image/design.png)

用户、订单、支付服务都会有对外暴露接口，故而它们各自有web层。web层app之间不会互相调用，它们只会与非web层的应用交互。

