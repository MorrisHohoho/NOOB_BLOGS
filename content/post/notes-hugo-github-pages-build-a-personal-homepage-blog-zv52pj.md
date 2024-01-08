---
title: '[折腾] Hugo+Github Pages 搭建个人主页+博客'
slug: notes-hugo-github-pages-build-a-personal-homepage-blog-zv52pj
url: /post/notes-hugo-github-pages-build-a-personal-homepage-blog-zv52pj.html
date: '2024-01-01 20:04:39'
lastmod: '2024-01-08 14:50:41'
toc: true
tags:
  - 折腾
categories:
  - 折腾
keywords: 折腾
description: hugo 101
isCJKLanguage: true
---

# [Notes] Hugo+Github Pages 搭建个人主页+博客

# 前置

* Github账号
* 安装Hugo，请参阅：[Installation | Hugo (gohugo.io)](https://gohugo.io/installation/)

下述内容均在 ubuntu 22.04 上操作。

# 个人主页

## 主题

要求：简洁好看，后续修改快。

虽然大家都在用 [Academic](https://themes.gohugo.io/themes/theme-academic-cv/) 这个主题，但是我个人比较喜欢简洁点的，比如：

* [devise | Hugo Themes (gohugo.io)](https://themes.gohugo.io/themes/devise/)
* [olOwOlo/hugo-theme-even](https://github.com/olOwOlo/hugo-theme-even)
* [michaelneuper/hugo-texify3](https://github.com/michaelneuper/hugo-texify3)

但是，这些主题虽然好看，有些冗余功能我并不想要。我只想要以下功能：

* 头像
* 社交按钮 (github, google scholar, CV）。
* 展示publications 和 teaching experience 的地方。

  * 并且能够带几个按钮，分别跳转到pdf, cite, google scholar, bibtex等
* 点击导航栏，跳转到对应界面。

鉴于以上要求，我打算自己写一个theme。 : )

<span style="font-weight: bold;" data-type="strong">参考文档：</span>

1. [动手写一个 Hugo 博客主题 | Yuanji&apos;s Blog (gimo.me)](https://blog.gimo.me/posts/creating-a-hugo-theme/)

    1. 附带阅读有hugo博客性能提升手段，还不错。
2. [Homepage template | Hugo (gohugo.io)](https://gohugo.io/templates/homepage/)

    1. Hugo官方template讲解
3. [Site variables | Hugo (gohugo.io)](https://gohugo.io/variables/site/)
4. 参考主题：[michaelneuper/hugo-texify3: A LaTeX-style hugo theme with the gruvbox color scheme for personal blogging (github.com)](https://github.com/michaelneuper/hugo-texify3)

### 美化-增加访客地图

利用clustrmaps一键生成javascripts脚本执行

### 美化-SVG图标

SVG图标可以在这里找到：[Simple Icons](https://simpleicons.org/?q=line), CV下载按钮样例写法：

```html
        <a href="/docs/CV.pdf">
        <svg class="header-item-svg" role="img" viewBox="0 0 24 24" xmlns="http://www.w3.org/2000/svg">
            <title>Read.cv</title>
            <path d="M20.8315 3.36707L8.66861 0.10804C6.98928 -0.34192 5.26312 0.65467 4.81314 2.334L0.94304 16.7773C0.49306 18.4568 1.48966 20.1829 3.16899 20.6328L15.3319 23.892C17.0112 24.3419 18.7373 23.3453 19.1874 21.666L23.0574 7.22254C23.5073 5.54321 22.5108 3.81705 20.8315 3.36707ZM6.33349 2.74138C6.55849 1.90171 7.42156 1.40341 8.26123 1.62841L20.4241 4.88744C21.2637 5.11242 21.762 5.9755 21.537 6.81516L17.6669 21.2586C17.4421 22.0983 16.5789 22.5966 15.7393 22.3715L3.57639 19.1125C2.73671 18.8875 2.23842 18.0245 2.4634 17.1849L6.33349 2.74138ZM8.9669 5.07654C8.54705 4.96404 8.11552 5.2132 8.00302 5.63302C7.89054 6.05285 8.13968 6.4844 8.55952 6.59689L17.6817 9.04116C18.1015 9.15366 18.5331 8.90451 18.6455 8.48468C18.7581 8.06483 18.5089 7.6333 18.0891 7.52081L8.9669 5.07654ZM6.98458 9.43392C7.09708 9.01409 7.52862 8.76493 7.94844 8.87743L17.0706 11.3217C17.4904 11.4342 17.7395 11.8657 17.6271 12.2856C17.5146 12.7054 17.0831 12.9546 16.6632 12.8421L7.54107 10.3978C7.12124 10.2853 6.87208 9.85375 6.98458 9.43392ZM6.93 12.6783C6.51017 12.5658 6.07862 12.815 5.96614 13.2348C5.85364 13.6546 6.1028 14.0861 6.52263 14.1987L11.8439 15.6246C12.2637 15.737 12.6952 15.4879 12.8077 15.0679C12.9202 14.6481 12.6711 14.2167 12.2513 14.1041L6.93 12.6783Z"/>
        </svg>
        </a>
```

### 美化-下划线动画

[下划线动画效果 (codepen.io)](https://codepen.io/awhitemouse/pen/xxGbbGx)

# 博客

## hugo挂在同一个github pages下的static资源问题

1. 首先，利用hugo和github page部署个人主页，其已经占用了`xxx.github.io`​
2. 其次，再用hugo和gitub page部署个人博客，主题是`Loveit`​。 其地址为`XXX.github.io/blog`​

根据hugo和loveit官方文档，图片，视频的等资源应该放在根目录下的`static`​文件夹，在md文件中以相对路径来引用:`/images/blog.png`​。

但是，博客的图片无法正常渲染，其路径指向`XXX.github.io/images/blog.png`​(个人主页的路径） 而不是`XXX.github.io/XXX/images/blog.png`​。

这时，只需改一下`hugo.toml`​的配置，使其生成绝对路径即可。

```go
baseURL = "http://example.org/NOOB_BLOGS" // 网站基本路径
canonifyURLS = true		//生成绝对路径
```

‍
