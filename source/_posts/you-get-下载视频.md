---
title: 如何使用you-get获取视频？
thumbnail: 
date: 2021-4-16 20:41:02
toc: true
tags:
    - 技术分享
categories:
    - [技术分享]
---

> 摘要
关于you-get的简单使用，方便获取网络上部分视频～
<!-- more -->


## **项目描述**

今天分享一个各大网站视频下载方法(封装好的pacong)。

目的是为了能够将视频下载到本地，方便学习翻阅；但有以下需要注意的地方：

- 下载下来的视频不能进行商业用途
- 下载时最好设置爬取时间间隔，以防对目标网站造成服务器压力倍增
- 本篇博文纯属技术分享(以bilibili网站视频为例)

### **you-get 安装**

```python
# you-get使用需要python3环境(建议使用anaconda)

pip install you-get
```

### **you-get 参数**

```python
# 命令行查看you-get参数

you-get -h
```

> 更多参数详细使用请参考：https://github.com/soimort/you-get

### **视频下载**

#### **单个视频下载**

```python
# 命令行下载

you-get -o /data/Download https://www.bilibili.com/video/bv111111?p=1
```

**参数解析**：

- -o /data/Download 表示输出路径，如果这个路径不存在，会自动新建
- 后面跟着是你需要下载视频的b站网址

```python
# 脚本下载

import sys
from you-get import common as you-get

directory = '/data/Dowmload'
url = 'https://www.bilibili.com/video/bv111111?p=1'
sys.argv = ['you-get' , '-o' , directory , url ]

you-get.main()
```

#### **多个视频批量下载**

```python
# 命令行

you-get -o /data/Download https://www.bilibili.com/video/bv111111?p=1 -l
```

**参数解析**：-l 参数将网址列表内的所有视频下载下来

```python
# 脚本下载

import sys
from you-get import common as you-get

directory = '/data/Dowmload'
url = 'https://www.bilibili.com/video/bv111111?p=1'
sys.argv = ['you-get' , '-o' , directory , url , '-l']

you-get.main()
```

</br>

## **写在最后**

以上为 **you-get** 下载b站视频的技术分享，更多参数使用请根据需求自行查阅。感谢阅读～