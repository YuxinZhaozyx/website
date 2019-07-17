---
# Documentation: https://sourcethemes.com/academic/docs/managing-content/

title: "My first post我的"
subtitle: "a subtitle"
summary: "anything"
authors: ["yuxinzhao"]
tags: ["test", "latex"]
categories: ["Learning"]
date: 2019-07-04T00:10:49+08:00
lastmod: 2019-07-04T00:10:49+08:00
featured: false
draft: true

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

diagram: true

---

{{% toc %}}

# hello

```python
import sys
a = 20
if a > 0:
	print(a)
```

```go
import "fmt"

type A struct {
	Key int `json:"key"`
	Value int `json:"value"`
}

func (a A) print() {
	fmt.Print("[",a.Key,":",a.Value,"]");
}
```

```shell
$ cd <mydir>
$ vi -i --help  path/to/file
vi path/to/another/file
```

# world


$$
sum = \sum_{i=1}^n i
$$


| a    | b    | c    |
| ---- | ---- | ---- |
| 1    | 2    | 3    |
| 4    | 5    | 6    |
| 7    | 8    | 9    |



a $\sum$ is sum.



{{<diagram>}}
graph LR
a -.extends.-> b
{{</diagram>}}

{{< list_tags >}}
