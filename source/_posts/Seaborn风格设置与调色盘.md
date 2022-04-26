---
title: Seaborn - 基础风格展示 & 调色盘
thumbnail: 
date: 2020-08-11 20:41:02
toc: true
tags:
    - Python库
categories:
    - [python,Seaborn]
---

> 摘要
Seaborn库的使用，包括一些基础风格的参数设置，以及调色盘(各色系的设置)～
<!-- more -->


## 写在前面

Matplotlib试着让简单的事情更加简单，困难的事情变得可能，而Seaborn就是让困难的东西更加简单。

实际上，Seaborn 是在matplotlib的基础上进行了更高级的 API 封装，从而使得作图更加容易

用Matplotlib最大的困难是其默认的各种参数，而Seaborn则完全避免了这一问题。

seaborn是针对统计绘图的，一般来说，seaborn能满足数据分析大部分的基本绘图需求，就能做出很具有吸引力的图，如果需要复杂的自定义图形，还是要Matplotlib。

- 官网：http://seaborn.pydata.org/index.html
- 安装：直接 **pip3 install seaborn** 即可

## seaborn风格设置

> Seaborn模块自带许多定制的主题和高级的接口，包括对图表整体颜色、比例等进行风格设置,包括颜色色板等，调用其系统风格即可实现各种不同风格的数据可视化

### 导入模块

> 本文基于jupyter notebook环境，先导入使用到的python模块，并创建一个正弦函数及绘制图表

```python
import pandas as pd
import numpy as np
import matplotlib.pyplot as plt
import seaborn as sns
%matplotlib inline
```

```python
# 创建正弦函数及图表

def sinplot(flip = 1):
    x = np.linspace(0,14,100)
    for i in range(1,7):
        plt.plot(x,np.sin(x + i * .5) * (7 - i) * flip)
sinplot()
```

![](https://cdn.jsdelivr.net/gh/minzvv/blogimg/seaborn-1/2_0.png)

### set

> set( ) ：通过设置参数可以用来设置背景，调色板等，最为常用。

```python
# tips：在jupyter notebook中，一旦设定了风格,所有图表的创建均自动带有风格,重启服务后才会重置为无风格状态
# 使用默认设置

sns.set()
sinplot()
plt.grid(linestyle = '--')
```

![](https://cdn.jsdelivr.net/gh/minzvv/blogimg/seaborn-1/3_0.png)

### set_style

> set_style() ：设置主题，即切换seaborn图表风格
>
> Seaborn有五个预设好的主题： darkgrid , whitegrid , dark , white ,和 ticks  默认： darkgrid

```python
fig = plt.figure(figsize = (6,6))

# whitegrid主题

sns.set_style('whitegrid')
ax1 = fig.add_subplot(2,1,1)
data = np.random.normal(size = (20,6)) + np.arange(6) / 2
sns.boxplot(data = data)
plt.title('style -- whitegrid')

# dark主题

sns.set_style('dark')
ax2 = fig.add_subplot(2,1,2)
sinplot()

```

![](https://cdn.jsdelivr.net/gh/minzvv/blogimg/seaborn-1/4_0.png)

### despine

> despine()  ：设置图表坐标轴，可以根据需求将坐标轴的展现与否进行设置，更好的讲故事～

```python
# despine()  -- 设置图表坐标轴

# sns.despine('fig=None', 'ax=None', 'top=True', 'right=True', 'left=False',  'bottom=False', 'offset=None', 'trim=False')

sns.set_style('ticks')

fig = plt.figure(figsize = (6,9))
plt.subplots_adjust(hspace=0.3)

ax1 = fig.add_subplot(3,1,1)
sinplot()

# 删除上、右坐标轴

sns.despine()

# 创建小提琴图

ax2 = fig.add_subplot(3,1,2)
sns.violinplot(data = data)

# sns.despine(offset = 10,trim = True)
# offset: 与坐标轴之间的偏移
# trim：为True时,将坐标轴限制在数据最大值最小值


ax3 = fig.add_subplot(3,1,3)
sns.boxplot(data = data,palette='deep')

# sns.despine(left = True,right = False)
# top,right,left,bottom: 布尔型,为True时不显示
```

```
<matplotlib.axes._subplots.AxesSubplot at 0xeddb830>
```

![](https://cdn.jsdelivr.net/gh/minzvv/blogimg/seaborn-1/5_2.png)

### axes_style

> axes_style() ：设置局部图表风格，配合with使用可以很方便的将想要表达的图表更加凸显出来

```python
# with：只在sns这个图表中设置风格，其他图表风格还是与之前设置的一致
# 设置局部图表风格,用with做代码块区分

with sns.axes_style('darkgrid'):
    plt.subplot(211)
    sinplot()


# 设置外部表格风格

sns.set_style('whitegrid')
plt.subplot(212)
sinplot()

```

![](https://cdn.jsdelivr.net/gh/minzvv/blogimg/seaborn-1/6_0.png)

### set_context

> set_context() ：设置显示的比例尺度，方便在不用显示器或不同分辨率下设置不同的显示比例
>
> seaborn内置四种显示比例：'paper'  'notebook'  'talk'  'poster'，左往右依次 变大

```python
# 默认为notebook

sns.set_context('talk')
sinplot()
```

![](https://cdn.jsdelivr.net/gh/minzvv/blogimg/seaborn-1/7_0.png)

## 调色盘

> - seaborn的调色盘用于对图表整体颜色、比例等进行风格设置,包括颜色色板等
> - 调色盘分为三类
>     1. **Sequential**：按顺序渐变的。 - Light colours for low data, dark for high data
>     2. **Diverging**：彼此之间差异变化较大的。 - Light colours for mid-range data, low and high contrasting dark colours
>     3. **Qualitative**：这个用于最大程度地显示不同类之间的差别。 - Colours designed to give maximum visual difference between classes
> - 一般调用seaborn内置系统风格和简单设置参数即能实现很炫酷的数据可视化

### color_palette

> seaborn.color_palette(palette=None, n_colors =None, desat =None)
>
> 1. **palette：None，string或sequence，可选，默认有6种主题：**deep,muted, pastel, bright, dark, colorblind
> 2. **n_colors：颜色个数**
> 3. **desat：每种颜色去饱和的比例**


```python
current_palette = sns.color_palette()
sns.palplot(current_palette)
```


![](https://cdn.jsdelivr.net/gh/minzvv/blogimg/seaborn-1/output_2_0.png)



```python
# sns.color_palette(pal, size=1)
# pal → 颜色风格, size → 颜色色块个数
# Reds/Reds_r  代表 颜色风格反转(不是所有颜色都可以反转)


sns.palplot(sns.color_palette('hls',8))
```


![](https://cdn.jsdelivr.net/gh/minzvv/blogimg/seaborn-1/output_3_0.png)

```python
# 分组颜色设置 - 'Paired'

sns.palplot(sns.color_palette('Paired',8))
```


![](https://cdn.jsdelivr.net/gh/minzvv/blogimg/seaborn-1/output_4_0.png)

### husl_palette

> 设置亮度和饱和度

```python
# 设置亮度、饱和度
# 1、husl_palette([n_colors,h,s,l])
# 2、hls_palette([n_colors,h,s,l])

# l  -  亮度、s  -  饱和度

sns.palplot(sns.hls_palette(8,l = .7,s =1))
```


![](https://cdn.jsdelivr.net/gh/minzvv/blogimg/seaborn-1/output_5_0.png)

### cubehelix_palette

> 按照线性增长计算,设置颜色

```python
# sns.cubehelix_palette('n_colors=6', 'start=0', 'rot=0.4', 'gamma=1.0', 'hue=0.8', 
#                      'light=0.85', 'dark=0.15', 'reverse=False', 'as_cmap=False')


sns.palplot(sns.cubehelix_palette(8,gamma = 2))
sns.palplot(sns.cubehelix_palette(8,start = 1.2,rot = -.5))
sns.palplot(sns.cubehelix_palette(8,start = 2,rot = 0,dark = 0,light = .95,reverse = True))

# 参数解析：
# n_colors：颜色个数
# start：值区间在0 - 3 , 开始颜色
# rot：颜色旋转角度
# gamma：颜色伽马值,值越大颜色越暗
# dark,light：值区间在0 - 1,颜色深浅
# reverse：布尔值,默认为False,由浅到深
```


![](https://cdn.jsdelivr.net/gh/minzvv/blogimg/seaborn-1/output_6_0.png)



![](https://cdn.jsdelivr.net/gh/minzvv/blogimg/seaborn-1/output_6_1.png)



![](https://cdn.jsdelivr.net/gh/minzvv/blogimg/seaborn-1/output_6_2.png)

### dark_palette / light_palette

> - 深色/浅色调色板
> - seaborn.dark_palette（color，n_colors =6，reverse =False，as_cmap = False，input ='rgb'）
>     1. **color** ：高值的颜色
>     2. **n_colors** ：颜色个数
>     3. **reverse** ：默认为False
>     4. **as_cmap** ：如果为True，则返回matplotlib colormap；为False，则返回list
>     5. **input**：{'rgb'，'hls'，'husl'，'xkcd'}

```python
# dark_palette() / light_palette()

# 按照green做浅色调色盘
sns.palplot(sns.light_palette('green'))

# 按照green做深色调色盘
sns.palplot(sns.dark_palette('green',reverse = True))


# 设置cmap为Greens风格

sns.palplot(sns.color_palette('Greens'))

# reverse → 转制颜色

sns.palplot(sns.color_palette('Greens_r'))
```


![](https://cdn.jsdelivr.net/gh/minzvv/blogimg/seaborn-1/output_7_0.png)

![](https://cdn.jsdelivr.net/gh/minzvv/blogimg/seaborn-1/output_7_1.png)

![](https://cdn.jsdelivr.net/gh/minzvv/blogimg/seaborn-1/output_7_2.png)

![](https://cdn.jsdelivr.net/gh/minzvv/blogimg/seaborn-1/output_7_3.png)

### diverging_palette

> diverging_palette ：创建分散颜色

```python
# 创建分散颜色

# sns.diverging_palette('h_neg', 'h_pos', 's=75', 'l=50', 'sep=10', 'n=6', 
#                      "center='light'", 'as_cmap=False')


sns.palplot(sns.diverging_palette(145,280,s = 85,l = 25,n = 7))

# 参数解析:
# h_neg, h_pos：起始/终止颜色值
# s ：值区间0 - 100,饱和度
# l ：值区间0 - 100,亮度
# n ：颜色个数
# center ：中心颜色为浅色还是深色'light' , 'dark' ,默认为light
```


![](https://cdn.jsdelivr.net/gh/minzvv/blogimg/seaborn-1/output_8_0.png)

### 应用

#### 热力图

```python
# 例子

plt.figure(figsize = (12,8))
x = np.arange(25).reshape(5,5)
cmap = sns.diverging_palette(200,20,sep = 20,as_cmap = True)
sns.heatmap(x,cmap=cmap)

# heatmap : 热力图
```


    <matplotlib.axes._subplots.AxesSubplot at 0x5b28c50>


![](https://cdn.jsdelivr.net/gh/minzvv/blogimg/seaborn-1/output_9_1.png)

#### 风格演示

```python
sns.set_style('whitegrid')

with sns.color_palette('PuBuGn_d'):
    plt.subplot(211)
    sinplot()

sns.set_palette('husl')
plt.subplot(212)
sinplot()
```


![](https://cdn.jsdelivr.net/gh/minzvv/blogimg/seaborn-1/output_11_0.png)

## 总结

本文介绍了python的绘图模块seaborn的基础认知，也就是绘图的基础风格设置与调色盘的基础知识，感谢阅读

本文版权归作者所有，欢迎转载，转载请注明出处和链接来源。