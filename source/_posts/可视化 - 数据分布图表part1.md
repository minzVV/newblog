---
title: 如何用图表直观显示数据的分布情况？
thumbnail: 
date: 2020-08-19 20:41:02
toc: true
tags:
    - 可视化图表
categories:
    - [数据分析,可视化]
---

> 摘要
数据分布可视化图表绘制，包括直方图、密度图、柱状图、折线图的一些实例～
<!-- more -->


## 写在前面

今天主要聊聊关于数据分布情况的可视化所用到的一部分图表

> - 什么是分布数据：定量化的数据，而非定性化的数据，一般指只是数值的数据
> - 什么是分布数据可视化：查看数据分布情况以及数据分布统计时做的图表可视化

## 直方图

> 直方图主要反映数据分布情况

### 导入模块


```python
import pandas as pd
import numpy as np
import matplotlib.pyplot as plt
import seaborn as sns
%matplotlib inline
```

### 设置绘图风格


```python
# 设置风格、尺度

sns.set_style('darkgrid')
sns.set_context('paper')

# 警告处理

import warnings
warnings.filterwarnings('ignore')
```

### 绘制直方图-1


```python
# 创建数据

rs = np.random.RandomState(10)
s = pd.Series(rs.randn(100) * 100)

# 绘制直方图及基本参数设置

sns.distplot(s,bins = 10,hist = True,kde = True,norm_hist = False,
            rug = True,vertical = False,
            color = 'y',label = 'distplot',axlabel = 'x')
plt.legend()

# 参数解析：
# bins → 箱子数量
# hist、kde → 是否显示箱/密度曲线
# norm_hist → 直方图是否按照密度来显示
# rug → 是否显示数据分布
# vertical → 是否水平显示
# color → 设置颜色
# label → 设置图例
# axlabel → x轴标注
```


    <matplotlib.legend.Legend at 0xf049090>


![](https://gitee.com/minzvv/blog_images/raw/master/seaborn-2-1/output_3_1.png)

### 绘制直方图-2

```python
sns.distplot(s,rug = True,

            # 设置数据频率分布
            rug_kws = {'color':'g'},

            # 设置密度曲线颜色、线宽、标注、线形
            kde_kws = {'color':'k','lw':1,'label':'KDE','linestyle':'--'},
             
            # 设置箱子风格、线宽、透明度、颜色
            # 风格包括：'bar' 'barstacked' 'step' 'stepfilled'
            hist_kws = {'histtype':'step','linewidth':'1','alpha':1,'color':'g'}
            )
```


    <matplotlib.axes._subplots.AxesSubplot at 0xf4fc9f0>


![](https://gitee.com/minzvv/blog_images/raw/master/seaborn-2-1/output_4_1.png)

## 密度图

> 密度图与直方图用途一致，都是用于反映数据的分布情况，可视化效果炫酷一些，看个人喜好选择

### 单样本密度图

```python
# 密度图 - kdeplot()

sns.kdeplot(s,

           # 是否填充
           shade = True,

           color = 'r',
           vertical = False)

sns.kdeplot(s,bw = 5,label = 'bw: 0.2',
           linestyle = '-',linewidth = 1.2,alpha = 0.5)
sns.kdeplot(s,bw = 20,label = 'bw: 2',
           linestyle = '-',linewidth = 1.2,alpha = 0.5)

# 参数解析：
# bw：控制拟合的程度,类似直方图的箱数


# 数据频率分布图

sns.rugplot(s,height = 0.1,color = 'k',alpha = 0.6)
```


    <matplotlib.axes._subplots.AxesSubplot at 0xca6b90>


![](https://gitee.com/minzvv/blog_images/raw/master/seaborn-2-1/output_5_1.png)

### 多样本密度图-1

```python
# 密度图 - 两个样本数据密度分布图

rs = np.random.RandomState(2)
df = pd.DataFrame(rs.randn(100,2),
                 columns = ['a','b'])
sns.kdeplot(df['a'],df['b'],

           # 是否显示颜色图例
           cbar = True, 

           # 是否填充
           shade = True,

           # 设置调色盘
           cmap = 'Reds', 

           # 最外围颜色是否显示
           shade_lowest = False,

           # 曲线个数(如果非常多,则会越平滑)
           n_levels = 10,
           )

# x,y轴设置

sns.rugplot(df['a'],color = 'g',axis = 'x',alpha = 0.5)
sns.rugplot(df['b'],color = 'r',axis = 'y',alpha = 0.5)
```


    <matplotlib.axes._subplots.AxesSubplot at 0x10bf0170>


![](https://gitee.com/minzvv/blog_images/raw/master/seaborn-2-1/output_6_1.png)

### 多样本密度图-2

```python
# 密度图 - 两个样本数据密度分布 

# 多个密度图

rs1 = np.random.RandomState(2)
rs2 = np.random.RandomState(5)
df1 = pd.DataFrame(rs1.randn(100,2)+2,columns=['a','b'])
df2 = pd.DataFrame(rs2.randn(100,2)-2,columns=['a','b'])

sns.kdeplot(df1['a'],df1['b'],cmap = 'Greens',shade = True,
           shade_lowest = False)
sns.kdeplot(df2['a'],df2['b'],cmap = 'Blues',shade = True,
           shade_lowest = False)

```


    <matplotlib.axes._subplots.AxesSubplot at 0x11147bf0>


![](https://gitee.com/minzvv/blog_images/raw/master/seaborn-2-1/output_7_1.png)

## 柱状图

> barplot  ：绘制柱状图 

### 分布统计柱状图

```python
# 置信区间：样本均值 + 抽样误差

titanic = sns.load_dataset('titanic')

plt.figure(figsize = (15,7))
sns.barplot(x = 'sex',y = 'survived',hue = 'class',data = titanic,
           palette = 'hls',
           order = ['male','female'],
           capsize = 0.05,
           saturation = 8,

           # 误差线颜色
           errcolor = 'gray',

           # 误差线宽度
           errwidth = 2,

           # 置信区间误差 → 0-100内,'sd' None
           ci = 'sd',
           )


# print(titanic.groupby(['sex','class']).mean()['survived'])
# print('----------------------------------------------')
# print(titanic.groupby(['sex','class']).std()['survived'])

titanic.head()
```

![bg-1](https://gitee.com/minzvv/blog_images/raw/master/seaborn-2-1/bg-1.png)

![](https://gitee.com/minzvv/blog_images/raw/master/seaborn-2-1/2_1.png)

### 柱状图拆分

```python
# 二次拆分

tips = sns.load_dataset('tips')

plt.figure(figsize = (15,7))
sns.barplot(x = 'day',y = 'total_bill',hue = 'sex',data = tips,
           palette = 'hls',edgecolor = 'w')
tips.groupby(['day','sex']).mean()
```

![bg-2](https://gitee.com/minzvv/blog_images/raw/master/seaborn-2-1/bg-2.png)

![](https://gitee.com/minzvv/blog_images/raw/master/seaborn-2-1/3_1.png)

### 实例应用

```python
# 例子
crashes = sns.load_dataset('car_crashes').sort_values('total',ascending = False)
print(crashes.head())

fig,axes = plt.subplots(figsize = (12,17))

sns.set_color_codes('pastel')
sns.barplot(x = 'total',y = 'abbrev',data = crashes,
           label = 'Total',color = 'b',edgecolor = 'w')

sns.set_color_codes('muted')
sns.barplot(x = 'alcohol',y = 'abbrev',data = crashes,
           label = 'Alcohol-involved',color = 'b',edgecolor = 'w')

axes.legend(ncol = 2,loc = 'lower-right')
sns.despine(left = True,bottom = True)
```

```
    total  speeding  alcohol  not_distracted  no_previous  ins_premium  \
40   23.9     9.082    9.799          22.944       19.359       858.97   
34   23.9     5.497   10.038          23.661       20.554       688.75   
48   23.8     8.092    6.664          23.086       20.706       992.61   
3    22.4     4.032    5.824          21.056       21.280       827.34   
17   21.4     4.066    4.922          16.692       16.264       872.51   

    ins_losses abbrev  
40      116.29     SC  
34      109.72     ND  
48      152.56     WV  
3       142.39     AR  
17      137.13     KY  
```

![](https://gitee.com/minzvv/blog_images/raw/master/seaborn-2-1/4_1.png)

### 计数柱状图

```python
# countplot() - 计数柱状图

plt.figure(figsize = (12,9))

# sns.countplot(x = 'class',hue = 'who',data = titanic,palette='magma')

sns.countplot(y = 'class',hue = 'who',data = titanic,palette='magma')

# x / y → 以x或者y轴绘图(横向、竖向)
```

```
<matplotlib.axes._subplots.AxesSubplot at 0x15dc5cb0>
```

![](https://gitee.com/minzvv/blog_images/raw/master/seaborn-2-1/5_1.png)

### 折线图

> pointplot：绘制折线图，置信区间的直观显示

```python
# pointplot()  -  折线图 - 置信区间估计

sns.pointplot(x = 'time',y = 'total_bill',hue = 'smoker',data = tips,
             palette = 'hls',
             dodge = True,
             join = True,
             markers = ['o','x'],linestyle = ['-','--'])

tips.groupby(['time','smoker']).mean()['total_bill']
```

```
time    smoker
Lunch   Yes       17.399130
        No        17.050889
Dinner  Yes       21.859429
        No        20.095660
Name: total_bill, dtype: float64
```

![](https://gitee.com/minzvv/blog_images/raw/master/seaborn-2-1/6_1.png)

## 总结

以上为研究数据分布时的可视化图表中较为常用的几种图表展示，看起来不难，实际上需要注意的是对图表背后数据结构的理解，图表可视化只是一种手段，真正重心还是在能否真正理解背后的数据逻辑，感谢阅读～

本文版权归作者所有，欢迎转载，转载请注明出处和链接来源。

