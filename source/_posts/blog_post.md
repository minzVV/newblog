---
title: 博客文章属性
date: 2021-05-26 14:58:34
tags:
    - blog
categories:
    - [blog,post]
---

> 摘要
博客文章属性解析..
<!-- more -->

# post 模板

## 本主题下post模板内容

> \---
>
> thumbnail:
>
> title: {{ title }}
>
> date: {{ date }}
>
> tags:
>
> -test1
>
> -test2
>
> categories: 
>
> -test3
>
> -test4
>
> toc: true
>
> recommend: 1
>
> keywords: categories-java
>
> uniqueId: {{ date }}/{{ title }}.html
>
> mathJax: false
>
> \---
>
> \> 摘要
>
> 首页显示摘要内容（替换成自己的）
>
> <!-- more -->
>
> 正文内容（替换成自己的）
>
> 
>
> 参考文章:  
>
> [参考链接]()



## 参数

| 参数        | 名称解析                                                     |
| ----------- | ------------------------------------------------------------ |
| thumbnail   | 文首缩略图，值：图片url                                     |
| title       | 文章标题                                                     |
| date        | 文章创建日期                                                 |
| tags        | 标签，无顺序，无层级                                         |
| categories  | 分类，有顺序，体现分类层级，例：[python,library]             |
| toc         | 启用章内索引，值：true/false                                 |
| recommend   | 文章推荐，值必须大于0,且值越大越靠前，相等取最新，最多5条    |
| keywords    | categories-java                                              |
| uniqueId    | {{ date }}/{{ title }}.html                                  |
| mathJax     | 数学公式渲染，值：false/true                                 |
| encrypt     | 文章加密，值：false/true；加密文章最好设置top：-1,将其排在最后 |
| `<!--more-->` | 文章折叠                                                     |
|             |                                                              |
|             |                                                              |
|             |                                                              |





参考文章:  

[参考链接 - Icarus用户指南](https://ppoffice.github.io/hexo-theme-icarus/Configuration/icarus用户指南-主题配置)












