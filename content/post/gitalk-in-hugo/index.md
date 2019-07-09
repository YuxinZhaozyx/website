---
# Documentation: https://sourcethemes.com/academic/docs/managing-content/

title: "Hugo博客集成Gitalk评论模块"
subtitle: ""
summary: "从使用Gitment到转用Gitalk作为评论模块的历程"
authors: ["yuxinzhao"]
tags: ["hugo", "academic", "gitalk", "comments", "github"]
categories: ["hugo"]
date: 2019-07-09T16:40:32+08:00
lastmod: 2019-07-09T16:40:32+08:00
featured: false
draft: false

# Featured image
# To use, add an image named `featured.jpg/png` to your page's folder.
# Focal points: Smart, Center, TopLeft, Top, TopRight, Left, Right, BottomLeft, Bottom, BottomRight.
image:
  caption: "Gitalk"
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

在[先前的文章](/post/gitment-in-hugo)中，我使用Gitment来作为我的博客的评论模块，但使用一段时间后发现了Gitment的一些缺点，针对这些缺点，我找到了比Gitment功能上更完善的Gitalk。

## 我为什么选择Gitalk?

在这之前我先说一下我为什么弃坑Gitment，Gitment本身的想法很好，使用github的issue来存储评论，避免了使用第三方的服务而第三方停止提供服务的风险(比如多说和网易云跟帖)。但它也有以下缺点：

+ 不兼容移动端
+ 不支持多语言
+ 不支持免打扰模式
+ 不支持快捷键提交

上述问题中对我来说最重要并最终让我决定更换评论模块的是Gitment不兼容移动端的缺点。

而Gitalk没有以上的缺点。

## Gitalk评论模块介绍

> Gitalk 是一个基于 GitHub Issue 和 Preact 开发的评论插件。

+ [项目地址](https://github.com/gitalk/gitalk)
+ [官方示例](https://gitalk.github.io/)

### 特性

- 使用 GitHub 登录
- 支持多语言 [en, zh-CN, zh-TW, es-ES, fr, ru]
- 支持个人或组织
- 免干扰模式（设置 `distractionFreeMode` 为 `true` 开启）
- 快捷键提交评论 （`ctrl + enter`）
- 支持移动端
- 自动初始化评论

## 基本使用

### 注册OAuth Application

需要先在github上注册一个OAuth Application，[点击此处](https://github.com/settings/applications/new)注册。

Callback URL 填写评论页面对应的域名，如`https://YuxinZhaozyx.github.io`。

{{% alert warning %}}
其他内容可以随便写，但Callback URL一定要填写正确。
{{% /alert %}}

注册成功后会得到一个Client ID和Client Secret，这将被用于之后的用户登录认证。

### 引入Gitalk

将以下代码添加到你的页面：

```html
<section id="comments">
	<div id="gitalkContainer"></div>
	<link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/gitalk@1/dist/gitalk.css">
	<script src="https://cdn.jsdelivr.net/npm/gitalk@1/dist/gitalk.min.js"></script>
	<script>
		var gitalk = new Gitalk({
			clientID: 'GitHub Application Client ID',
            clientSecret: 'GitHub Application Client Secret',
            repo: 'GitHub repo',
            owner: 'GitHub repo owner',
            admin: ['GitHub repo owner and collaborators, only these guys can initialize github issues'],
            id: location.pathname,      // Ensure uniqueness and length less than 50
            distractionFreeMode: false  // Facebook-like distraction free mode
		});
		gitalk.render('gitalkContainer');
	</script>
</section>
```

{{% alert warning %}}

`id`的长度要限制在50个字符内，但也不可为空字符串。

{{% /alert %}}

#### 参数说明

<style type="text/css">
	table.tableizer-table {
		font-size: 12px;
		border: 1px solid #CCC; 
		font-family: Arial, Helvetica, sans-serif;
	} 
</style>
<table class="tableizer-table">
<thead>
    <tr class="tableizer-firstrow">
        <th>参数</th>
        <th>类型</th>
        <th>可选</th>
        <th>默认值</th>
        <th>说明</th>
    </tr>
</thead>
<tbody>
    <tr>
        <td>`clientID`</td>
        <td>String</td>
        <td>no</td>
        <td>`null`</td>
        <td>GitHub Application Client ID</td>
    </tr>
    <tr>
        <td>`clientSecret`</td>
        <td>String</td>
        <td>no</td>
        <td>`null`</td>
        <td>GitHub Application Client Secret</td>
    </tr>
    <tr>
        <td>`repo`</td>
        <td>String</td>
        <td>no</td>
        <td>`null`</td>
        <td>存放评论的gitHub仓库</td>
    </tr>
    <tr>
        <td>`owner`</td>
        <td>String</td>
        <td>no</td>
        <td>`null`</td>
        <td>存放评论的gitHub仓库的所有者</td>
    </tr>
    <tr>
        <td>`admin`</td>
        <td>Array</td>
        <td>no</td>
        <td>`[ owner ]`</td>
        <td>允许初始化评论的用户（repo的所有者和合作者）</td>
    </tr>
    <tr>
        <td>`id`</td>
        <td>String</td>
        <td>yes</td>
        <td>`location.href`</td>
        <td>页面的唯一标识，长度必须小于50</td>
    </tr>
    <tr>
        <td>`number`</td>
        <td>Number</td>
        <td>yes</td>
        <td>-1</td>
        <td>页面的issueID标识，若未定义number属性则使用id进行定位</td>
    </tr>
    <tr>
        <td>`labels`</td>
        <td>Array</td>
        <td>yes</td>
        <td>`['Gitalk']`</td>
        <td>GitHub issue的标签</td>
    </tr>
    <tr>
        <td>`title`</td>
        <td>String</td>
        <td>yes</td>
        <td>`document.title`</td>
        <td>GitHub issue的标题</td>
    </tr>
    <tr>
        <td>`body`</td>
        <td>String</td>
        <td>yes</td>
        <td>`location.href + header.meta[description]`</td>
        <td>GitHub issue的内容</td>
    </tr>
    <tr>
        <td>`language`</td>
        <td>String</td>
        <td>yes</td>
        <td>`navigator.language || navigator.userLanguage`</td>
        <td>语言，支持[en, zh-CN, zh-TW]</td>
    </tr>
    <tr>
        <td>`perPage`</td>
        <td>Number</td>
        <td>yes</td>
        <td>10</td>
        <td>每次加载的评论数，最多100</td>
    </tr>
    <tr>
        <td>`distractionFreeMode`</td>
        <td>Boolean</td>
        <td>yes</td>
        <td>`false`</td>
        <td>类似Facebook评论框的全屏遮罩效果.</td>
    </tr>
    <tr>
        <td>`pagerDirection`</td>
        <td>String</td>
        <td>yes</td>
        <td>`'last'`</td>
        <td>评论排序方式， `last`为按评论创建时间倒叙，`first`为按创建时间正序。</td>
    </tr>
    <tr>
        <td>`createIssueManually`</td>
        <td>Boolean</td>
        <td>yes</td>
        <td>`false`</td>
        <td>如果当前页面没有相应的 isssue 且登录的用户属于 admin，则会自动创建 issue。如果设置为 true，则显示一个初始化页面，创建 issue 需要点击 init 按钮。</td>
    </tr>
    <tr>
        <td>`proxy`</td>
        <td>String</td>
        <td>yes</td>
        <td>https://cors-anywhere.herokuapp.com/https://github.com/login/oauth/access_token</td>
        <td>GitHub oauth请求到反向代理，为了支持CORS</td></tr>
    <tr>
        <td>`flipMoveOptions`</td>
        <td>Object</td>
        <td>yes</td>
        <td>
            <code>
            { <br>
            &emsp; staggerDelayBy: 150, <br>
            &emsp; appearAnimation: 'accordionVertical', <br>
            &emsp; enterAnimation: 'accordionVertical', <br>
            &emsp; leaveAnimation: 'accordionVertical', <br>
            }
            </code>
        </td>
        <td>评论的参考动画</td>
    </tr>
    <tr>
        <td>`enableHotKey`</td>
        <td>Boolean</td>
        <td>yes</td>
        <td>`true`</td>
        <td>启用快捷键(cmd|ctrl + enter) 提交评论</td></tr>
</tbody>
</table>

### 初始化评论

Gitalk不需要像Gitment一样点初始化按钮初始化(除非将`createIssueManually`选项设置为`true`)，只需要`admin`中的任何一名管理员登录账号即可自动初始化。

## Gitalk在Hugo Academic主题下的配置

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
{{ if eq .Site.Params.gitalk.on true }}
<section id="comments">
	<div id="gitalkContainer"></div>
	<link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/gitalk@1/dist/gitalk.css">
	<script src="https://cdn.jsdelivr.net/npm/gitalk@1/dist/gitalk.min.js"></script>

	<script>
		var gitalk = new Gitalk({
			clientID: {{ .Site.Params.gitalk.clientID }},
			clientSecret: {{ .Site.Params.gitalk.clientSecret }},
			repo: {{ .Site.Params.gitalk.repo }},
			owner: {{ .Site.Params.gitalk.owner }},
			admin: {{ .Site.Params.gitalk.admin }},
			id: '{{ .Params.Date }}',      // Ensure uniqueness and length less than 50
			labels: {{ .Site.Params.gitalk.labels }},
			distractionFreeMode: {{ .Site.Params.gitalk.distractionFreeMode }},  // Facebook-like distraction free mode
			pagerDirection: {{ .Site.Params.gitalk.pagerDirection }},
			createIssueManually: {{ .Site.Params.gitalk.createIssueManually }},
			enableHotKey: {{ .Site.Params.gitalk.enableHotKey }},
			flipMoveOptions: {
				staggerDelayBy: {{ .Site.Params.gitalk.flipMoveOptions.staggerDelayBy }},
				appearAnimation: {{ .Site.Params.gitalk.flipMoveOptions.appearAnimation }},
				enterAnimation: {{ .Site.Params.gitalk.flipMoveOptions.enterAnimation }},
				leaveAnimation: {{ .Site.Params.gitalk.flipMoveOptions.leaveAnimation }},
			},
		});
		gitalk.render('gitalkContainer');
	</script>
</section>
{{ end }}
```

在 `config/_default/params.toml`末尾添加以下配置。

```toml
# Config comments of gitalk
[gitalk]
  on = true
  clientID = "ec5806c0144b70a1b32e"
  clientSecret = "aadae41a3747ffa5b36fd0e13c7e84026db07e0c"
  repo  = "YuxinZhaozyx.github.io"
  owner = "YuxinZhaozyx"
  admin = ["YuxinZhaozyx"]
  labels = ["Comments"]  # default to ["Gitalk"]
  distractionFreeMode = false
  pagerDirection = 'last' # set to 'last' or 'first'
  createIssueManually = false
  enableHotKey = true  # cmd | ctrl + enter to commit comment
  [gitalk.flipMoveOptions]
    staggerDelayBy = 150
    appearAnimation = 'elevator'
    enterAnimation = 'elevator'
    leaveAnimation = 'elevator'
```

至此，评论功能配置完成。

