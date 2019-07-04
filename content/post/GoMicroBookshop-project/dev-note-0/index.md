---
# Documentation: https://sourcethemes.com/academic/docs/managing-content/

title: "GoMicroBookshop项目开发笔记-0"
subtitle: "准备开发环境"
summary: "GoMicroBookshop项目是我学习micro编写的练手项目-准备开发环境"
authors: ["yuxinzhao"]
tags: ["golang", "micro", "go-micro", "micro service"]
categories: ["project-note"]
date: 2019-07-04T23:02:54+08:00
lastmod: 2019-07-04T23:02:54+08:00
featured: false
draft: false

# Featured image
# To use, add an image named `featured.jpg/png` to your page's folder.
# Focal points: Smart, Center, TopLeft, Top, TopRight, Left, Right, BottomLeft, Bottom, BottomRight.
image:
  caption: "Gopher"
  focal_point: ""
  preview_only: false

# Projects (optional).
#   Associate this post with one or more of your projects.
#   Simply enter your project's folder or file name without extension.
#   E.g. `projects = ["internal-project"]` references `content/project/deep-learning/index.md`.
#   Otherwise, set `projects = []`.
projects: ["GoMicroBookshop"]
---

# 项目简介

## 业务模块

- **用户**，users
- **库存**，inventory
- **权限**，auth
- **订单**，orders
- **支付**，payment

## 服务抽象结构

![img](https://github.com/YuxinZhaozyx/GoMicroBookshop/raw/master/image/design.png)

用户、订单、支付服务都会有对外暴露接口，故而它们各自有web层。web层app之间不会互相调用，它们只会与非web层的应用交互。

## 准备工作

- Golang环境 [安装](https://golang.google.cn/)
- gRPC [安装](https://grpc.io/docs/quickstart/go.html)
- Consul
- Micro

```shell
## 安装go-micro
go get github.com/micro/go-micro

## 安装micro
go get github.com/micro/micro
```

- mysql

还有其它一些会用到的库或组件，但不是基础依赖，需要时再安装。

## 涉及技术与库

Golang，gRPC，Mysql，Redis，Docker，K8s，Go-micro/Micro

## 搭建平台

win10

