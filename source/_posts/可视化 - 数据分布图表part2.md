---
title: 一眼就get到数据分布情况的图表？
thumbnail: 
date: 2020-08-22 20:41:02
toc: true
tags:
    - 可视化图表
categories:
    - [数据分析,可视化]
---

> 摘要
数据分布可视化图表绘制，包括散点图、蜂窝图、箱型图、小提琴图、LV图表的一些实例～
<!-- more -->


## 前言

上篇文章介绍了数据分布情况的可视化的四种图表 (直方图、密度图、柱状图、折线图)的 展示方法，下面将介绍另外几种直观显示数据分布情况的可视化图表

## 散点图

> 也称为「点图」、「散布图」或「X-Y 点图」。
>
> 所谓的散点图 (Scatterplot) 就是在笛卡尔座标上放置一系列的数据点，用来显示两个变量的数值（每个轴上显示一个变量），并检测两个变量之间的关系或相关性是否存在。
>
> 可以很直观的观察到数据的分布情况

### 导入模块


```python
import pandas as pd
import numpy as np
import matplotlib.pyplot as plt
import seaborn as sns
%matplotlib inline

# 警告处理

import warnings
warnings.filterwarnings('ignore')
```

### 散点图 + 分布图


```python
# 1、综合散点图 - jointplot()

rs = np.random.RandomState(2)
df = pd.DataFrame(rs.randn(200,2),columns = ['a','b'])

sns.jointplot(x = df['a'],y = df['b'],
             data = df,
             color = 'k',
             s = 50,edgecolor = 'w',linewidth = 1,

             # 类型有'reg','resid','kde','hex','scatter'
             kind = 'scatter',
             space = 0.3,
             size = 8,
             ratio = 5,

             # 类型为kde时不能设置marginal_kws
             marginal_kws = dict(bins = 15,rug = True)
             )
```

    <seaborn.axisgrid.JointGrid at 0x5759510>


![](https://cdn.jsdelivr.net/gh/minzvv/blogimg/seaborn-2-2/output_2_2.png)

### 六边形图 / 蜂窝图

```python
# 构建数据

df = pd.DataFrame(rs.randn(500,2),columns = ['a','b'])

with sns.axes_style('white'):
    sns.jointplot(x = df['a'],y = df['b'],kind = 'hex',
                 color = 'k',
                 marginal_kws = dict(bins = 20))
```

![](https://cdn.jsdelivr.net/gh/minzvv/blogimg/seaborn-2-2/output_3_1.png)

### 密度图 + 散点图

```python
# 密度图

rs = np.random.RandomState(15)
df = pd.DataFrame(rs.randn(300,2),columns = ['a','b'])

g = sns.jointplot(x = df['a'],y = df['b'],data = df,
                 kind = 'kde', color = 'k',
                 shade_lowest = False)

g.plot_joint(plt.scatter,c = 'w', s = 30,linewidth = 1,marker = '+')
```


    <seaborn.axisgrid.JointGrid at 0x1431ec90>


![](https://cdn.jsdelivr.net/gh/minzvv/blogimg/seaborn-2-2/output_4_1.png)

### 综合散点图

```python
# 综合散点图 - JointGrid()
# 可拆分绘制的散点图  plot_joint() / ax_marg_x.hist() / ax_marg_y.hist()

sns.set_style('white')

tips = sns.load_dataset('tips')
print('tips.head()')

g = sns.JointGrid(x = 'total_bill',y = 'tip',data = tips)

g.plot_joint(plt.scatter,color = 'm',edgecolor = 'white')
g.ax_marg_x.hist(tips['total_bill'],color = 'b',alpha = 0.6,
                bins = np.arange(0,60,3))
g.ax_marg_y.hist(tips['tip'],color = 'r',alpha = 0.6,
                 orientation = 'horizontal',bins = np.arange(0,12,1))

from scipy import stats
g.annotate(stats.pearsonr)

plt.grid(linestyle = '--')
```

![](https://cdn.jsdelivr.net/gh/minzvv/blogimg/seaborn-2-2/output_5_2.png)

### 拆分绘制 - 散点图

> 分别绘制散点图和直方图

```python
# 可拆分绘制的散点图
# plot_joint() / plot_marginals()

# 直方图

g = sns.JointGrid(x = 'total_bill' , y = 'tip' , data = tips)

g = g.plot_joint(plt.scatter,color = 'g',s = 40,edgecolor = 'white')
plt.grid(linestyle = '--')

g.plot_marginals(sns.distplot,kde = True,color = 'g')
```

    <seaborn.axisgrid.JointGrid at 0x154c0690>


![](https://cdn.jsdelivr.net/gh/minzvv/blogimg/seaborn-2-2/output_6_2.png)

### 拆分绘制 - 密度图

> 分别绘制密度图和面积图

```python
# 可拆分绘制的散点图
# plot_joint() / plot_marginals()

# 密度图

g = sns.JointGrid(x = 'total_bill' , y = 'tip' , data = tips)

g = g.plot_joint(sns.kdeplot,cmap = 'Reds_r')
plt.grid(linestyle = '--')

g.plot_marginals(sns.kdeplot,shade = True,color = 'r')
```


    <seaborn.axisgrid.JointGrid at 0x154b13b0>


![](https://cdn.jsdelivr.net/gh/minzvv/blogimg/seaborn-2-2/output_7_1.png)

### 矩阵散点图

> 散点图矩阵是组织成网格（矩阵）形式的散点图集合。每个散点图显示一对变量之间的关系。

```python
# 矩阵散点图 - pairplot()

# 定义风格
sns.set_style('white')

# 引入数据

iris = sns.load_dataset('iris')
print(iris.head())

sns.pairplot(iris,

            # 散点图 / 回归分布图('scatter','reg')
            kind = 'scatter', 

            # 直方图 / 密度图'hist','kde'
            diag_kind = 'hist', 

            # 按照某一字段进行分类
            hue = 'species',

            # 设置调色板
            palette = 'husl',  

            # 设置不同系列的点样式(参考分类个数)
            markers = ['o','s','D'],
            size = 3)
```

       sepal_length  sepal_width  petal_length  petal_width species
    0           5.1          3.5           1.4          0.2  setosa
    1           4.9          3.0           1.4          0.2  setosa
    2           4.7          3.2           1.3          0.2  setosa
    3           4.6          3.1           1.5          0.2  setosa
    4           5.0          3.6           1.4          0.2  setosa
    
    <seaborn.axisgrid.PairGrid at 0x15511310>


![](https://cdn.jsdelivr.net/gh/minzvv/blogimg/seaborn-2-2/output_8_3.png)

#### 局部变量对比

```python
# 只提取局部变量进行对比

sns.pairplot(iris,vars = ['sepal_width','sepal_length'],
            kind = 'reg',diag_kind = 'kde',
            hue = 'species',palette = 'husl',size = 5)
```

    <seaborn.axisgrid.PairGrid at 0x1d94b510>


![](https://cdn.jsdelivr.net/gh/minzvv/blogimg/seaborn-2-2/output_9_2.png)

#### 多类显示

```python
# 其他参数设置

sns.pairplot(iris , diag_kind = 'kde',markers = '+',
            plot_kws = dict(s = 50,edgecolor = 'b',linewidth = 1),
            diag_kws = dict(shade = True))
```

    <seaborn.axisgrid.PairGrid at 0x14feffb0>


![](https://cdn.jsdelivr.net/gh/minzvv/blogimg/seaborn-2-2/output_10_2.png)

### 拆分绘制 - 散点图2

```python
# 可拆分绘制的散点图 - PairGrid()

# map_diag() / map_offdiag()

g = sns.PairGrid(iris,hue = 'species',palette = 'hls',
                vars = ['sepal_length','sepal_width','petal_length','petal_width'],
                size = 3)

# 创建一个绘图表格区域,设置好x,y对应的数据,按照species分类

g.map_diag(plt.hist,
          histtype = 'barstacked',linewidth = 1,edgecolor = 'w')

# 对角线图表,plt.hist / sns.kdeplot
g.map_offdiag(plt.scatter,edgecolor = 'w',s = 40,linewidth = 1)

# 其他图表

g.add_legend()
# 添加图例
```

    <seaborn.axisgrid.PairGrid at 0x21652e90>


![](https://cdn.jsdelivr.net/gh/minzvv/blogimg/seaborn-2-2/output_11_2.png)



### 分类散点图

#### 绘制分类散点图


```python
# stripplot() - 按照不同类别对样本数据进行分布散点图绘制

sns.set_style('whitegrid')
sns.set_context('paper')

tips = sns.load_dataset('tips')
print(tips.head())

sns.stripplot(x = 'day',

             # 设置分组统计字段
             y = 'total_bill', 

             # 数据分布统计字段
             data = tips,
             jitter = True,   

             # 当点数据重合较多时,用该参数调整,可以设置间距如：jitter = 0.1
             size = 5,edgecolor = 'w',linewidth = 1,marker = 'o')
```

       total_bill   tip     sex smoker  day    time  size
    0       16.99  1.01  Female     No  Sun  Dinner     2
    1       10.34  1.66    Male     No  Sun  Dinner     3
    2       21.01  3.50    Male     No  Sun  Dinner     3
    3       23.68  3.31    Male     No  Sun  Dinner     2
    4       24.59  3.61  Female     No  Sun  Dinner     4
    
    <matplotlib.axes._subplots.AxesSubplot at 0xc12130>


![](https://cdn.jsdelivr.net/gh/minzvv/blogimg/seaborn-2-2/output_14_2.png)

#### 二次分类

```python
# 通过hue参数再分类

sns.stripplot(x = 'sex',y = 'total_bill',hue = 'day',
             data = tips,jitter = True)
```


    <matplotlib.axes._subplots.AxesSubplot at 0xf54fed0>


![](https://cdn.jsdelivr.net/gh/minzvv/blogimg/seaborn-2-2/output_15_1.png)

#### 二次拆分

```python
# 二次拆分

sns.stripplot(x = 'sex',y = 'total_bill',hue = 'day',
             data = tips,jitter = True,

             # 设置调色盘
             palette = 'Set2', 

             # 是否拆分
             dodge = True
             )
```


    <matplotlib.axes._subplots.AxesSubplot at 0xf5524d0>


![](https://cdn.jsdelivr.net/gh/minzvv/blogimg/seaborn-2-2/output_16_1.png)

#### 二次筛选

```python
# 筛选分类类别

print(tips['day'].value_counts())

sns.stripplot(x = 'total_bill',y = 'day',data = tips,jitter = True,
             order = ['Sat','Sun'])
# order → 筛选类别
```

    Sat     87
    Sun     76
    Thur    62
    Fri     19
    Name: day, dtype: int64
    
    <matplotlib.axes._subplots.AxesSubplot at 0xb840b0>


![](https://cdn.jsdelivr.net/gh/minzvv/blogimg/seaborn-2-2/output_17_2.png)

### 分簇散点图

```python
# swarmplot() - 分簇散点图

sns.swarmplot(x = 'day',y = 'total_bill',data = tips,
             size = 5,edgecolor = 'w',linewidth = 1,marker = 'o',
             palette = 'Reds')
```


    <matplotlib.axes._subplots.AxesSubplot at 0xa53c70>


![](https://cdn.jsdelivr.net/gh/minzvv/blogimg/seaborn-2-2/output_18_1.png)

## 箱型图

>  boxplot ：又称为盒须图、盒式图或箱线图，是一种用作显示一组数据分散情况资料的统计图。因形状如箱子而得名

### 初步绘制

```python
# 定义风格

sns.set_style('whitegrid')
sns.set_context('paper')

# 引入数据

tips = sns.load_dataset('tips')

sns.boxplot(x = 'day',y = 'total_bill',data = tips,
           linewidth = 2,
           width = 0.5,

           # 异常点大小
           fliersize = 3, 

           # 调色板
           palette = 'hls',

           # 设置IQR
           whis = 1.5, 

           # 是否以中值做凹槽
           notch = False,
           order = ['Thur','Fri','Sat','Sun'])

# sns.swarmplot(x = 'day',y = 'total_bill',data = tips,color = 'k',size = 3,alpha = 0.7)
```

```
<matplotlib.axes._subplots.AxesSubplot at 0x614e2b0>
```

![](https://cdn.jsdelivr.net/gh/minzvv/blogimg/seaborn-2-2/2_1.png)

### 二次分类

```python
# 二次分类

sns.boxplot(x = 'day',y = 'total_bill',data = tips,hue = 'sex',palette = 'Reds')

# sns.swarmplot(x = 'day',y = 'total_bill',data = tips,color = 'k',size = 3,alpha = 0.7)
```

```
<matplotlib.axes._subplots.AxesSubplot at 0x62316f0>
```

![](https://cdn.jsdelivr.net/gh/minzvv/blogimg/seaborn-2-2/3_1.png)



## 小提琴图

> 小提琴图其实是箱线图与核密度图的结合，通过小提琴图能更容易看出哪些位置的密度较高，即数据分布的区域

### 初步绘制

```python
# 小提琴图 - violinplot()

sns.violinplot(x = 'day',y = 'total_bill',data = tips,

              # 线宽
              linewidth = 2, 

              # 箱之间的间隔比例
              width = 0.8, 

              # 调色盘
              palette = 'hls', 
              order = ['Thur','Fri','Sat','Sun'],

              # 小提琴图的宽度：area-面积相同,count-样本数量决定宽度,width-宽度一样
              scale = 'area',

              # 设置小提琴图边线的平滑度,值越大越平滑
              gridsize = 50,

              # 设置内部显示类型：'box' 'quartile' 'point' 'stick' None
              inner = 'box',              
              # bw = 0.8
              )
```

```
<matplotlib.axes._subplots.AxesSubplot at 0x10c7eab0>
```

![](https://cdn.jsdelivr.net/gh/minzvv/blogimg/seaborn-2-2/4_1.png)

### 二次分类

```python
# 二次分类

sns.violinplot(x = 'day',y = 'total_bill',data = tips,
              hue = 'smoker',palette = 'muted',

              # 设置是否拆分小提琴图
              split = True, 
              inner = 'box')
```

```
<matplotlib.axes._subplots.AxesSubplot at 0x110572d0>
```

![](https://cdn.jsdelivr.net/gh/minzvv/blogimg/seaborn-2-2/5_1.png)

### 混合图表

> 小提琴图结合散点图

```python
# 小提琴图 - 结合散点图

sns.violinplot(x = 'day',y = 'total_bill',data = tips,palette = 'hls',inner = None)
sns.swarmplot(x = 'day',y = 'total_bill',data = tips,color = 'w',alpha = 0.5)
# 插入散点图
```

```
<matplotlib.axes._subplots.AxesSubplot at 0x11224ed0>
```

![](https://cdn.jsdelivr.net/gh/minzvv/blogimg/seaborn-2-2/6_1.png)

## LV图表

```python
# lvplot() - lv图表

plt.figure(figsize = (15,7))
sns.lvplot(x = 'day',y = 'total_bill',data = tips,palette = 'mako',
          width = 0.8,
          hue = 'smoker',
          linewidth = 12,

          # 设置框的大小 → 'linear','exonential','area'
          scale = 'area',

          # 设置框的数量 → 'proportion','tukey','trustworthy'
          k_depth = 'proportion',
          )

sns.swarmplot(x = 'day',y = 'total_bill',data = tips,color = 'k',size = 6,alpha = 0.8)
```

```
<matplotlib.axes._subplots.AxesSubplot at 0x11263f30>
```

![](https://cdn.jsdelivr.net/gh/minzvv/blogimg/seaborn-2-2/7_1.png)

## 写在最后

这两篇博文列举了对数据分布情况的基础可视化图表，当然只是很基础的图表展示，记录下来以便随时翻阅，查漏补缺，还是那句话，任何图表可视化重点是数据结构与其逻辑，真正理解了数据才能更好的选择图表去展示，感谢阅读～

本文版权归作者所有，欢迎转载，转载请注明出处和链接来源。