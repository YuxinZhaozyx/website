---
# Documentation: https://sourcethemes.com/academic/docs/managing-content/

title: "Hugo博客集成Gitment评论模块"
subtitle: "Gitment使用踩坑教程"
summary: "由于国内无法访问Disqus，hugo-academic主题自带的disqus评论功能无法使用，故而寻求其他评论方法，最后找到了gitment，但也踩了很多的坑。"
authors: ["yuxinzhao"]
tags: ["hugo", "academic", "gitment", "comments"]
categories: ["hugo"]
date: 2019-07-05T17:20:59+08:00
lastmod: 2019-07-05T17:20:59+08:00
featured: false
draft: false

# Featured image
# To use, add an image named `featured.jpg/png` to your page's folder.
# Focal points: Smart, Center, TopLeft, Top, TopRight, Left, Right, BottomLeft, Bottom, BottomRight.
image:
  caption: "github"
  focal_point: ""
  preview_only: false

# Projects (optional).
#   Associate this post with one or more of your projects.
#   Simply enter your project's folder or file name without extension.
#   E.g. `projects = ["internal-project"]` references `content/project/deep-learning/index.md`.
#   Otherwise, set `projects = []`.
projects: []
---

## 前言

我使用的Hugo主题Academic自带Disqus评论模块支持，但由于国内无法访问Disqus，因此我开始寻找其他评论模块，最终选择了Gitment评论模块。

## 我为什么选择Gitment？

在选择之前我们先看一下有哪些选项可以选择。

+ **Disqus**:  国外使用较多的评论组件，但国内需要代理才能访问。

+ **多说**:  国内最多用户使用的评论系统，但已于2017年6月停止提供服务。
+ **网易云跟帖**:  网易提供的评论系统，但也于2017月8月停止提供服务。
+ **畅言**:  搜狐提供的评论组件，功能丰富，体验优异；但必须进行域名备案。只要域名备过案就可以通过审核，简单问题复杂化。
+ **Gitment**:   国人[I'm Sun](<https://imsun.net/>)编写的开源评论模块，创新性地将评论放置在github的issue中，作者目前已不再维护。
+ **CommentHub**:  受Gitment启发也是将评论存储在github issue中的评论系统，解决Gitment会在前端暴露Client ID和Client Secret可能造成的安全问题，改进成在后端服务处理业务和存储证书，通过iframe实现评论功能。

已停止服务的多说、网易云跟帖和国内无法访问的Disqus无法使用，故排除在选择范围之外。畅言由于需要备案，步骤繁琐不想用。剩下的Gitment和ComementHub实际上很像，CommentHub看上去会更安全一些，因为它不会像Gitment一样暴露Client ID和Client Secret，但其实即便别人获取了我们的Client ID和Client Secret，没有我的github账号依然无法使用，而且只能在我指定的网址才能用，故Gitment还是比较安全的。

最终我在两者中选择了Gitment (因为CommentHub有点难看)。

## Gitment评论模块介绍

> Gitment is a comment system based on GitHub Issues, which can be used in the frontend without any server-side implementation.
>
> Gitment 是一款基于 GitHub Issues 的评论系统。支持在前端直接引入，不需要任何后端代码。可以在页面进行登录、查看、评论、点赞等操作，同时有完整的 Markdown / GFM 和代码高亮支持。尤为适合各种基于 GitHub Pages 的静态博客或项目页面。

+ [项目地址](<https://github.com/imsun/gitment>)

+ [官方示例](<https://imsun.github.io/gitment/>)

## 基本使用

### 1. 注册OAuth Application

需要先在github上注册一个OAuth Application，[点击此处](https://github.com/settings/applications/new)注册。

Callback URL 填写评论页面对应的域名，如`https://YuxinZhaozyx.github.io`。

{{% alert warning %}}
其他内容可以随便写，但Callback URL一定要填写正确。
{{% /alert %}}

注册成功后会得到一个Client ID和Client Secret，这将被用于之后的用户登录认证。

### 2. 引入Gitment

将一下代码添加到你的页面：

```html
<div id="container"></div>
<link rel="stylesheet" href="https://imsun.github.io/gitment/style/default.css">
<script src="https://imsun.github.io/gitment/dist/gitment.browser.js"></script>
<script>
var gitment = new Gitment({
  id: '页面 ID', // 可选。默认为 location.href
  owner: '你的 GitHub ID',
  repo: '存储评论的 repo',
  oauth: {
    client_id: '你的 client ID',
    client_secret: '你的 client secret',
  },
})
gitment.render('container')
</script>
```

{{% alert warning %}}

`id`的长度要限制在50个字符内，但也不可为空字符串。详情见[此处](#问题汇总)。

{{% /alert %}}



{{% alert warning %}}

上述代码为gitment作者的示例代码，但作者已停止维护，网址已不可用，可用以下代码代替。

```html
<link rel="stylesheet" href="https://www.wenjunjiang.win/css/gitment.css">
<script src="https://www.wenjunjiang.win/js/gitment.js"></script>
```

或者

```html
<link rel="stylesheet" href="https://cdn.jsdelivr.net/gh/theme-next/theme-next-gitment@1/default.css">
<script src="https://cdn.jsdelivr.net/gh/theme-next/theme-next-gitment@1/gitment.browser.js"></script>
```

目前来看后者更稳定一点。

{{% /alert %}}



{{% alert note %}}

以上方式会使用最新版本的Gitment，若希望引用确定版本的Gitment，需要使用npm进行安装。

```shell
$ npm install --save gitment
```

使用操作详见[Gitment Option](https://github.com/imsun/gitment#options)。

{{% /alert %}}

### 3. 初始化评论

页面发布后，你需要访问页面并使用你的 GitHub 账号登录（请确保你的账号是第二步所填 repo 的 owner），点击初始化按钮。

之后其他用户即可在该页面发表评论。

{{% alert note %}}

要先**登录**才能初始化，未登录初始化前可能会显示`Error: Comments Not initialized`错误，属正常现象。

{{% /alert %}}

## 问题汇总

### 1. `Error: Not Found`

+ owner或者repo配置错误，注意名字和参考名字的大小写。

+ 注意不可设置 `id : ""`。

### 2. `Error: Comments Not initialized`

+ 还没有登录，登录之后初始化就可以了。
+ 注册OAuth Application时Authorization Callback URL指定地址错误。

### 3. `Error: Validation Failed` 

+ `id`太长，导致初始化失败。解决方案见问题4。
  + id`的长度要限制在50个字符内，但也不可为空字符串
  + github issue的标签label有长度限制，label的最大长度为50个字符
  + `id`的作用是唯一标识每一篇文章。
  + 在gitment创建的issue里，每个issue有两个label，其中一个是`gitment`，另一个是`id`指定的值。因此`id`受到label的限制。

### 4. 在一篇文章下看到另一篇文章的issue

+ 两篇文章的`id`重复

#### 解决方案

  将文章的`id`设置为文章的创建时间，即可保证`id`长度不超过50个字符且文章评论不重复。

  ```js
  id: {{ .Params.Date }}
  ```

  {{% alert warning %}}

  不要复制前面的某一篇论文而忘记修改时间，否则评论还是会交错的。

  {{% /alert %}}

## Gitment的汉化版本

只需到模板里将原来定义CSS和JS的那两行改成：

```html
<link rel="stylesheet" href="https://billts.site/extra_css/gitment.css">
<script src="https://billts.site/js/gitment.js"></script>
```

## Gitment在Hugo Academic主题下的配置

在hugo建立的根目录下创建目录`layouts/partials/`，再在新创建的目录下创建`comments.html`用于覆盖Academic主题的`comments.html`。

```shell
$ cd <your-hugo-website-root>
$ mkdir -p layouts/partials/
$ vi layouts/partials/comments.html
```

{{% alert note %}}

注意是在hugo建立的根目录而不是修改`themes/academic/`主题下的文件。

{{% /alert %}}

在`layouts/partials/comments.html`中输入以下代码：

```html
{{ if .Site.Params.gitment.language }}
<section id="comments">
	<div id="gitmentContainer"></div>
	{{ if eq .Site.Params.gitment.language "zh" }}
	<link rel="stylesheet" href="https://billts.site/extra_css/gitment.css">
	<script src="https://billts.site/js/gitment.js"></script>
	{{ else }}
	<link rel="stylesheet" href="https://cdn.jsdelivr.net/gh/theme-next/theme-next-gitment@1/default.css">
	<script src="https://cdn.jsdelivr.net/gh/theme-next/theme-next-gitment@1/gitment.browser.js"></script>
	{{ end }}

	<script>
		var gitment = new Gitment({
			id: '{{ .Params.Date }}',
			owner: {{ .Site.Params.gitment.owner }},
			repo: {{ .Site.Params.gitment.repo }},
			oauth: {
				client_id: {{ .Site.Params.gitment.clientID }},
				client_secret: {{ .Site.Params.gitment.clientSecret }},
			},
		});
		gitment.render('gitmentContainer');
	</script>
</section>

{{ end }}
```

在 `config/_default/params.toml`末尾添加以下配置。

```toml
# Config comments of gitment
[gitment]
  language = "en" # set "" to disable comment, "zh" to Chinese, "en" to English
  owner = "<your-github-name>"
  repo  = "<the-repo-to-store-comments>"
  clientID = "<your-client-ID>"
  clientSecret = "<your-client-secret>"
```

至此，评论功能配置完成。

## 参考引用

[1]  [Gitment：使用 GitHub Issues 搭建评论系统 ](https://imsun.net/posts/gitment-introduction/)

[2]  [Gitment评论功能接入踩坑教程](https://ihtcboy.com/2018/02/25/2018-02-25_Gitment评论功能接入踩坑教程/)

[3]  [Error: Comments Not Initialized # 95](https://github.com/imsun/gitment/issues/95)

[4]  [Validation Failed ID长度问题建议 #116](https://github.com/imsun/gitment/issues/116)

[5]  [[object ProgressEvent] #170](https://github.com/imsun/gitment/issues/170)

[6]  [网站无法访问了？ #102](https://github.com/imsun/gitment/issues/102)