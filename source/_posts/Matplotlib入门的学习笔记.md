---
title: Matplotlib - 常用图表 & python表格样式
thumbnail: 
date: 2020-08-09 20:41:02
toc: true
tags:
    - Python库
categories:
    - [python,Matplotlib]
---

> 摘要
Matplotlib库的使用，包括常用图表的绘制，以及表格样式
<!-- more -->

## 前言

**Matplotlib** 是一个 Python 的 2D绘图库，它以各种硬拷贝格式和跨平台的交互式环境生成出版质量级别的图形。



通俗地说，**matplotlib**可能是数据分析中最常用的绘图Python包了。它可以对Python中的数据进行快速的可视化，并以多种格式输出。接下来，我们将以互动的方式介绍matplotlib中的大多数情况
<br>

## 导入模块

本文基于**Jupiter notebook**环境下进行举例介绍，先导入使用到的python模块

```python
import pandas as pd
import numpy as np
import matplotlib.pyplot as plt
%matplotlib inline
```

## 初步认识

先从一维数组和二维数组来简单认识一下**Matplotlib**

### 1、一维数组

​		借助**numpy**模块构建一个一维数组，并生成**折线图**


```python
# 数据构建

ts = pd.Series(np.random.randn(1000),index=pd.date_range('1/1/2018',periods=1000))
ts = ts.cumsum()

# 参数设置

ts.plot(
       # line(折线图),bar(柱状图),barh(柱状图-横),kde(密度图)
       kind = 'line',

       # 图例标签，Dataframe格式以列名为label
       label = 'nb',

       # 风格字符串，这里包括了linestyle，marker，color
       style = '--g.',
       color = 'b',
       alpha = 0.6,
       grid = True,

       # 是否以index作为横坐标轴
       use_index = True,

       # 横坐标旋转角度
       rot = 45,

       # y轴界限
       ylim = [-50,50],

       # y轴刻度值
       yticks = list(range(-50,50,10)),

       figsize = (12,8),
       title = 'normal',

       # 是否显示图例，一般直接用plt.legend()
       legend = True
       )
```


    <matplotlib.axes._subplots.AxesSubplot at 0x19d3c90>


![](https://minzvvblog.oss-cn-shenzhen.aliyuncs.com/matplotlib/output_1_1.jpg)

### 2、二维数组

```python
# 数据构建

df = pd.DataFrame(np.random.randn(1000,4),index=ts.index,columns=list('abcd'))

# 返回累计和
df = df.cumsum()

# 参数设置

df.plot(kind = 'line',
       style = '--.',
       grid = True,
       alpha = 0.3,
       use_index = True,
       rot = 30,
       figsize = (12,8),
       title = True,
       legend = True,
       subplots = False,
       colormap = 'Greens')
```


    <matplotlib.axes._subplots.AxesSubplot at 0xd2cc490>


![](https://minzvvblog.oss-cn-shenzhen.aliyuncs.com/matplotlib/output_2_1.png)


## 常用图表

下面介绍**matplotlib**在数据分析中的一些常用图表的绘制以及参数设置

### 柱状图

plt.plot(kind = 'bar/barh') / plt.bar()


```python
# 数据、画布构建

fig,axes = plt.subplots(4,1,figsize = (18,16))
s = pd.Series(np.random.randint(0,10,16),index=list('abcdefghijklmnop'))
df = pd.DataFrame(np.random.rand(10,3),columns=['a','b','c'])


# 单系列

s.plot(kind = 'bar',ax = axes[0], rot = 0)

# 多系列

df.plot(kind = 'bar',ax = axes[1])


# 多系列堆叠图

df.plot(kind = 'bar',stacked = True,ax = axes[2])
# 另一种写法

df.plot.bar(ax = axes[3])
# plt.axis('tight')
```


    <matplotlib.axes._subplots.AxesSubplot at 0x14008550>


![](https://minzvvblog.oss-cn-shenzhen.aliyuncs.com/matplotlib/output_4_1.png)

### 堆叠图

```python
plt.figure(figsize = (12,8))
x = np.arange(10)
y1 = np.random.rand(10)
y2 = -np.random.rand(10)

plt.bar(x,y1,width = 1,facecolor = 'yellowgreen',edgecolor = 'white',yerr = y1*0.1)
plt.bar(x,y2,width = 1,facecolor = 'lightskyblue',edgecolor = 'white',yerr = y2*0.1)

# 参数解析：
# width:宽度比例
# facecolor:柱状图里填充的颜色，edgecolor:边框的颜色
# left - 每个柱x轴左边界，bottom - 每个柱y轴下边界 → bottom扩展可化成甘特图 Gantt Chart
# align：决定整个bar图分布，默认left表示从左边界开始绘制，center会将图绘制在中间位置
# fig.tight_layout()

plt.grid()


for i,j in zip(x,y1):
    plt.text(i-0.15,0.05,'%.2f' % j ,color = 'white')
for i,j in zip(x,y2):
    plt.text(i-0.15,-0.1,'%.2f' % -j ,color = 'white')

# zip() 函数用于将可迭代对象作为参数，将对象中对应的元素打包成一个个元组，然后返回由这些元组组成的列表
```


![](https://minzvvblog.oss-cn-shenzhen.aliyuncs.com/matplotlib/output_5_0.png)

### 外嵌图表

图与表的结合，能更全面的展示数据，在展示了数据的可读性的同时，也具备了

```python
# 外嵌图表 plt.table()

data = [[ 66386, 174296, 75131, 577908, 32015],
       [ 35713,21312,32133,12414,57565],
       [ 36588,56856,58756,98356,87589],
       [35356,36432,68886,87875,95636],
       [21415,56936,68587,98793,87935]]
columns = ('Freeze','Wind','Flood','Quake','Hail')
rows = ['%d year' % x for x in (100,50,20,10,5)]
df = pd.DataFrame(data,columns=columns,index=rows)
print(df)

df.plot(kind = 'bar',grid = True,colormap = 'Blues_r',stacked = True,figsize = (12,8))

# 创建堆叠图

plt.table(cellText = data,
         cellLoc = 'center',
         cellColours = None,
         rowLabels = rows,
         rowColours = plt.cm.BuPu(np.linspace(0,0.5,5))[::-1],#BuPu相当于colormap
         colLabels = columns,
         colColours = plt.cm.Reds(np.linspace(0,0.5,5))[::-1],
         rowLoc = 'right',
         loc = 'bottom')

# 参数解析：
# cellText：表格文本
# cellLoc：cell内文本对齐位置
# rowLabels：行标签
# colLabels：列标签
# rowLoc：行标签对齐位置
# loc：表格位置 → left,right,top,bottom

plt.xticks([])
```

              Freeze    Wind  Flood   Quake   Hail
    100 year   66386  174296  75131  577908  32015
    50 year    35713   21312  32133   12414  57565
    20 year    36588   56856  58756   98356  87589
    10 year    35356   36432  68886   87875  95636
    5 year     21415   56936  68587   98793  87935
    
    ([], <a list of 0 Text xticklabel objects>)


![](https://minzvvblog.oss-cn-shenzhen.aliyuncs.com/matplotlib/output_6_2.png)

### 面积图


```python
# 面积图: plot.area


fig,axes = plt.subplots(2,1,figsize = (12,8))
df1 = pd.DataFrame(np.random.rand(10,4),columns = ['a','b','c','d'])
df2 = pd.DataFrame(np.random.randn(10,4),columns = ['a','b','c','d'])

df1.plot.area(colormap = 'Greens_r',alpha = 0.6,ax = axes[0])
df2.plot.area(stacked = False,colormap = 'Set2',alpha = 0.6,ax = axes[1])
# stacked : 是否堆叠，默认情况下，区域图被堆叠

# 为了产生堆积面积图，每列必须是全部为正值或全部为负值

# 当数据有NaN时，自动填充0，所以图标签需要清洗掉缺失值
```


    <matplotlib.axes._subplots.AxesSubplot at 0x115591f0>


![](https://minzvvblog.oss-cn-shenzhen.aliyuncs.com/matplotlib/output_8_1.png)

### 填图

```python
# 填图

x1 = np.linspace(0,5*np.pi,1000)
y3 = np.sin(x1)
y5 = np.sin(2*x1)
axes[1].fill_between(x1,y3,y5,color = 'b',alpha = 0.5,label = 'area')

#填充两个函数之间的区域，使用fill_between函数

for i in range(2):
    axes[i].legend()
    axes[i].grid()
```


![](https://minzvvblog.oss-cn-shenzhen.aliyuncs.com/matplotlib/output_9_0.png)

### 饼图

```python
# 饼图

s = pd.Series(3*np.random.rand(4),index=['a','b','c','d'],name = 'series')
plt.axis('equal')
plt.pie(s,
       # 指定每部分的偏移量
       explode=[0.1,0,0,0],

       # 标签 
       labels = s.index,

       # 颜色
       colors = ['r','g','b','c'],

       # 饼图上的数据标签显示方式
       autopct='%.2f%%',

       # 每个饼切片的中心和通过autopct生成的文本开始之间的比例
       pctdistance=0.6,

       # 被画饼标记的直径，默认值：1.1
       labeldistance=1.2,

       # 阴影
       shadow = True,

       # 开始角度
       startangle=0,

       # 半径
       radius=1.5,

       # 图框
       frame=False
       )
# counterclock:指定指针方向，顺时针或者逆时针

print(s)
```

    a    0.791267
    b    2.460981
    c    2.458505
    d    2.777957
    Name: series, dtype: float64



![](https://minzvvblog.oss-cn-shenzhen.aliyuncs.com/matplotlib/output_10_1.png)

### 直方图

```python
# 直方图  plt.hist()

s = pd.Series(np.random.randn(1000))
s.hist(
      # 箱子个数
      bins = 20,

      # 风格：bar,barstacked,step,stepfilled
      histtype = 'bar',

      # 对齐方式：left,right,mid
      align = 'mid',

      # 水平还是垂直：'horizontal','vertical'
      orientation = 'vertical',

      alpha = 0.5,

      # 标准化
      normed = True
      )

s.plot(kind = 'kde',style = 'k--')
```


    <matplotlib.axes._subplots.AxesSubplot at 0x15226d10>


![](https://minzvvblog.oss-cn-shenzhen.aliyuncs.com/matplotlib/output_11_1.png)

### 堆叠直方图

```python
# 堆叠直方图

plt.figure(num = 1)
df = pd.DataFrame({'a':np.random.randn(1000) + 1,
                  'b':np.random.randn(1000),
                  'c':np.random.randn(1000) - 1,
                  'd':np.random.randn(1000) - 2},
                 columns=['a','b','c','d'])
df.plot.hist(stacked = True,
            bins = 20,
            colormap = 'Greens_r',
            alpha = 0.5,
            grid = True,
            edgecolor = 'black')

df.hist(bins = 50)
# 直接生成多个直方图
```


    array([[<matplotlib.axes._subplots.AxesSubplot object at 0x1510E290>,
            <matplotlib.axes._subplots.AxesSubplot object at 0x15745CD0>],
           [<matplotlib.axes._subplots.AxesSubplot object at 0x15761AF0>,
            <matplotlib.axes._subplots.AxesSubplot object at 0x1577CA10>]],
          dtype=object)


    <Figure size 432x288 with 0 Axes>

![](https://minzvvblog.oss-cn-shenzhen.aliyuncs.com/matplotlib/output_12_2.png)



![](https://minzvvblog.oss-cn-shenzhen.aliyuncs.com/matplotlib/output_12_3.png)

### 散点图

```python
# 散点图 

# plt.scatter('x', 'y', 's=None', 'c=None', 'marker=None', 'cmap=None', 'norm=None','vmin=None', 'vmax=None', 

# 'alpha=None', 'linewidths=None', 'verts=None', 'edgecolors=None', '*', 'data=None', '**kwargs'）


plt.figure(figsize = (8,6))
x = np.random.randn(1000)
y = np.random.randn(1000)

plt.scatter(x,y,marker = '.',
           s = np.random.randn(1000)*100,
           c = y*100,
           cmap = 'Reds',
           alpha = 0.8)
plt.grid()
# vmin,vmax：亮度设置，标量
```


![](https://minzvvblog.oss-cn-shenzhen.aliyuncs.com/matplotlib/output_13_0.png)

### 矩阵散点图

```python
# 矩阵散点图


df = pd.DataFrame(np.random.randn(100,4),columns=['a','b','c','d'])
pd.scatter_matrix(df,figsize = (10,6),
                 marker = 'o',
                 diagonal='kde',
                 alpha = 0.5,
                 range_padding=0.1)
# diagonal:({'hist','kde'})，必须只能在两个中选其一， → 每个指标的频率图

# range_padding : (float,可选),图像在x轴、y轴原点附近的留白，值越大，留白距离越大，图像远离坐标原点
```

![](https://minzvvblog.oss-cn-shenzhen.aliyuncs.com/matplotlib/output_14_3.png)

### 极坐标图

```python
# 极坐标图


s = pd.Series(np.arange(20))
theta = np.arange(0,2*np.pi,0.02)
# print(s.head())

# print(theta[:10])


# 创建数据

fig = plt.figure(figsize = (12,8))
ax1 = plt.subplot(121,projection = 'polar')
ax2 = plt.subplot(122)
# projection = 'polar' → 创建极坐标图

# ax = fig.add_subplot(111,polar = True)

ax1.plot(theta,theta*3,linestyle = '--',lw = 1)
ax1.plot(s,linestyle = '--',marker = '.',lw = 2)
ax2.plot(theta,theta*3,linestyle = '--',lw = 1)
ax2.plot(s)
plt.grid()
# 创建极坐标图，参数1为角度(弧度制)，参数2为value

# lw → 线宽
```


![](https://minzvvblog.oss-cn-shenzhen.aliyuncs.com/matplotlib/output_15_0.png)



```python
# 极坐标参数设置

theta = np.arange(0,2*np.pi,0.02)
plt.figure(figsize = (8,4))
ax1 = plt.subplot(121,projection = 'polar')
ax2 = plt.subplot(122,projection = 'polar')
ax1.plot(theta,theta/6,'--',lw = 2)
ax2.plot(theta,theta/6,'--',lw = 2)

# set_theta_direction : 坐标轴正方形，默认逆时针
ax2.set_theta_direction(-1)

# set_thetagrids : 设置极坐标角度网格线显示及标签 → 网格和标签数量一致
ax2.set_thetagrids(np.arange(0.0,360.0,90),['a','b','c','d'])

# set_rgrids : 设置极径网格线显示，其中参数必须是正数
ax2.set_rgrids(np.arange(0.2,2,0.4))

# set_theta_offset : 设置角度偏移,逆时针,弧度制
ax2.set_theta_offset(np.pi/2)

# set_rlim : 设置显示的极径范围
ax2.set_rlim(0.2,1.2)

# set_rmax : 设置显示的极径最大值
ax2.set_rmax(2)

# set_rticks : 设置极径网格线的显示范围
ax2.set_rticks(np.arange(0.1,1.5,0.2))
```



![](https://minzvvblog.oss-cn-shenzhen.aliyuncs.com/matplotlib/output_16_1.png)

### 雷达图

```python
# 雷达图1 - 极坐标的折线图/填图

# plt.plot画出的雷达图首尾不相连

plt.figure(figsize = (12,8))

ax1 = plt.subplot(111,projection = 'polar')
ax1.set_title('radar map\n')
ax1.set_rlim(0,12)

data1 = np.random.randint(1,10,10)
data2 = np.random.randint(1,10,10)
data3 = np.random.randint(1,10,10)
theta = np.arange(0,2*np.pi,2*np.pi/10)

ax1.plot(theta,data1,'--',label = 'data1')
ax1.fill(theta,data1,alpha = 0.2)
ax1.plot(theta,data2,'--',label = 'data1')
ax1.fill(theta,data2,alpha = 0.2)
ax1.plot(theta,data3,'--',label = 'data1')
ax1.fill(theta,data3,alpha = 0.2)
```


    [<matplotlib.patches.Polygon at 0xe585fd0>]

![](https://minzvvblog.oss-cn-shenzhen.aliyuncs.com/matplotlib/output_17_2.png)

### 雷达图进阶使用

​	雷达图与极坐标图、填图的组合使用

```python
# 雷达图2 - 极坐标的折线图/填图 

# plt.polar() → 首尾闭合

labels = np.array(['a','b','c','d','e','f'])
dataLenth = 6
data1 = np.random.randint(0,10,6)
data2 = np.random.randint(0,10,6)

angles = np.linspace(0,2*np.pi,dataLenth,endpoint=False)  #分割圆周长
data1 = np.concatenate((data1,[data1[0]]))   #闭合
data2 = np.concatenate((data2,[data2[0]]))
angles = np.concatenate((angles,[angles[0]]))

plt.polar(angles,data1,'o-',linewidth = 1)
plt.fill(angles,data1,alpha = 0.25)
plt.polar(angles,data2,'o-',linewidth = 1)
plt.fill(angles,data2,alpha = 0.25)

plt.thetagrids(angles * 180/np.pi,labels)
plt.ylim(0,10)
```


    (0, 10)




![](https://minzvvblog.oss-cn-shenzhen.aliyuncs.com/matplotlib/output_18_1.png)

### 极轴图

```python
# 极轴图 - 极坐标的柱状图


plt.figure(figsize = (12,8))

ax1 = plt.subplot(111,projection = 'polar')
ax1.set_title('rader map\n')
ax1.set_rlim(0,12)

data = np.random.randint(1,10,10)
theta = np.arange(0,2*np.pi,2*np.pi/10)

bar = ax1.bar(theta,data,alpha = 0.5)
for r,bar in zip(data,bar):
    bar.set_facecolor(plt.cm.jet(r/10.))
plt.thetagrids(np.arange(0.0,360.0,90),[])
```


    (<a list of 8 Line2D thetagridline objects>,
     <a list of 4 Text thetagridlabel objects>)


![](https://minzvvblog.oss-cn-shenzhen.aliyuncs.com/matplotlib/output_19_1.png)

### 箱型图

​	**箱型图**：又称**箱线图**、**盒须图**，是一种用作显示一组**数据分散情况**资料的统计图

```python
# 1、中位数：一组数据平均分成两份，中间的数

# 2、上四分位数Q1：是将序列平均分成四份，计算(n+1)/4与(n-1)/4两种，一般使用(n+1)/4

# 3、下四分位数Q3：是将序列平均分为四份，计算(1+n)/4*3 = 0.75

# 4、内限 → T形的盒须就是内限，最大值区间Q3+1.5IQR,最小区间Q1-1.5IQR(IQR = Q3 - Q1)

# 5、外限 → T形的盒须就是内限，最大值区间Q3+3IQR,最小区间Q1-3IQR(IQR = Q3 - Q1)

# 6、异常值：内限之外→中度异常，外限之外→极度异常

fig,axes = plt.subplots(2,1,figsize = (12,8))
df = pd.DataFrame(np.random.rand(10,5),columns=['a','b','c','d','e'])
color = dict(boxes = 'DarkGreen',whiskers = 'DarkOrange',medians = 'DarkBlue',
            caps = 'Gray')

df.plot.box(ylim = [0,1.2],
           grid = True,
           color = color,
           ax = axes[0])

df.plot.box(vert = False,
           positions = [1,4,5,6,8],
           ax = axes[1],
           grid = True,
           color = color)
```


    <matplotlib.axes._subplots.AxesSubplot at 0x109bccd0>


![](https://minzvvblog.oss-cn-shenzhen.aliyuncs.com/matplotlib/output_20_1.png)

#### 箱型图-1

```python
# .boxplot

df = pd.DataFrame(np.random.rand(10,5),columns=['a','b','c','d','e'])
plt.figure(figsize = (12,8))

# 构建箱型图

f = df.boxplot(
    # 异常点的形状
    sym = 'o',

    # 是否垂直
    vert = True,

    # IQR，默认1.5，可以设置区间[5,95]，代表上下边缘为数据的95%和5%
    whis = 1.5,

    # 上下四分位框内是否填充
    patch_artist = True,

    # 是否有均值线及其形状
    meanline = False,showmeans = True,

    # 是否显示箱线
    showbox = True,

    # 是否显示边缘线
    showcaps = True,

    # 是否显示异常值
    showfliers = True,

    # 中间箱体是否缺口
    notch = False,

    # 返回类型为字典
    return_type = 'dict'
)

plt.title('boxplot')
print(f)

# 其余参数设置

for box in f['boxes']:
    # 箱体边框颜色
    box.set(color = 'b',linewidth = 1)

    # 箱体内部填充颜色
    box.set(facecolor = 'b',alpha = 0.5)


for whisker in f['whiskers']:
    whisker.set(color = 'k',linewidth = 0.5,linestyle = '-')
for cap in f['caps']:
    cap.set(color = 'gray',linewidth = 2)
for median in f['medians']:
    median.set(color = 'DarkBlue',linewidth = 2)
for flier in f['fliers']:
    flier.set(marker = 'o',color = 'y',alpha = 0.5)

# 参数解析：
# boxes：箱线
# medians：中位线的横线
# whiskers：从box到error bar之间的竖线
# fliers：异常值
# caps：error bar横线
# means：均值的横线
```

![](https://minzvvblog.oss-cn-shenzhen.aliyuncs.com/matplotlib/output_21_1.png)

#### 箱型图-2

```python
# plt.boxplot()  绘制

# 分组汇总

df = pd.DataFrame(np.random.rand(10,2),columns = ['col1','col2'])
df['x'] = pd.Series(['a','a','a','a','a','b','b','b','b','b'])
df['y'] = pd.Series(['a','b','a','b','a','b','a','b','a','b'])
print(df.head())

# df.boxplot(by = 'x')

df.boxplot(column = ['col1','col2'],by = ['x','y'])
# columns:按照数据的列分子图

# by：按照列分组做箱型图
```

           col1      col2  x  y
    0  0.507773  0.223859  a  a
    1  0.128340  0.482120  a  b
    2  0.955340  0.912310  a  a
    3  0.170645  0.949025  a  b
    4  0.821798  0.059242  a  a
    
    array([<matplotlib.axes._subplots.AxesSubplot object at 0x13B3BA30>,
           <matplotlib.axes._subplots.AxesSubplot object at 0x1115FED0>],
          dtype=object)


![](https://minzvvblog.oss-cn-shenzhen.aliyuncs.com/matplotlib/output_22_2.png)

### python的表格样式

#### 1、表格样式创建

```python
# 样式创建

# 1、styler.applymap: elementwise → 按元素方式处理df，也就是按照每一个值处理

# 2、styler.apply: column- / row- / tabel_wise  →  按行/列处理df


df = pd.DataFrame(np.random.randn(10,4),columns=['a','b','c','d'])
sty = df.style
# print(sty,type(sty))


def color_neg_red(val):
    if val < 0:
        color = 'red'
    else:
        color = 'black'
    return('color:%s' % color)
df.style.applymap(color_neg_red)

# 创建样式方法，使得小于0的数变成红色

# style.applymap() → 自动调用其中的函数
```

![](https://minzvvblog.oss-cn-shenzhen.aliyuncs.com/matplotlib/image-20190814101035687.png)

#### 2、样式处理


```python
# 按列/行处理样式：style.apply()

df = pd.DataFrame(np.random.randn(10,4),columns=['a','b','c','d'])
sty = df.style
def highlight_max(s):
    is_max = s == s.max()
    lst = []
    for v in is_max:
        if v:
            lst.append('background-color: yellow')
        else:
            lst.append('')
    return(lst)

# df.style.apply(highlight_max,axis = 0,subset = ['b','c'])

# subset:选择索引进行函数处理

df.style.apply(highlight_max,axis = 1,
             subset = pd.IndexSlice[2:5,['b','d']])

# df[2:5].style.apply(highlight_max,subset = ['b','d'])
```

![](https://minzvvblog.oss-cn-shenzhen.aliyuncs.com/matplotlib/image-20190814101222459.png)

#### 3、内容显示


```python
# 表格显示控制


df = pd.DataFrame(np.random.randn(10,4),columns=['a','b','c','d'])

# df.head().style.format('{:.2%}')    #显示百分比

# df.head().style.format('{:.4f}')    #显示小数点

# df.head().style.format("{:+.2f}")   #显示正负数


# 分列显示

df.head().style.format({'b':"{:.2%}",'c':"{:+.3f}",'d':"{:.3f}"})
```

![](https://minzvvblog.oss-cn-shenzhen.aliyuncs.com/matplotlib/image-20190814101425327.png)

### 表格进阶

#### 1、应用 — 空值定位


```python
# 内置样式调用

# 1、定位空值

df = pd.DataFrame(np.random.rand(5,4),columns=list('abcd'))
df['a'][2] = np.nan
df.style.highlight_null(null_color = 'yellow')
```

![](https://minzvvblog.oss-cn-shenzhen.aliyuncs.com/matplotlib/image-20190814101514145.png)

#### 2、应用 — 色彩映射


```python
# 2、色彩映射

df = pd.DataFrame(np.random.rand(10,4),columns=list('abcd'))
df.style.background_gradient(cmap = 'Greens',axis = 1,low = 0,high = 1)
```

![](https://minzvvblog.oss-cn-shenzhen.aliyuncs.com/matplotlib/image-20190814101555712.png)

#### 3、应用 — 条形图显示


```python
# 3、条形图

df = pd.DataFrame(np.random.rand(10,4),columns=list('abcd'))
df.style.bar(subset = ['a','b'],color = '#d65f5f',width = 100)
# width: 最长长度在格子的占比
```

![](https://minzvvblog.oss-cn-shenzhen.aliyuncs.com/matplotlib/image-20190814101656082.png)

#### 4、应用 — 分段式显示


```python
# 分段式构建样式

df = pd.DataFrame(np.random.rand(10,4),columns=list('abcd'))
df['a'][3] = np.nan
df['b'][7] = np.nan
df.style.\
    bar(subset = ['a','b'],color = '#d33f5f',width = 100).\
    highlight_null(null_color = 'yellow')
```

![](https://minzvvblog.oss-cn-shenzhen.aliyuncs.com/matplotlib/image-20190814101817617.png)

## 总结

以上为数据分析中matplotlib库中的常用图表的绘制以及各个图表的参数说明，记录下来方便查漏补缺的同时，可供随时翻阅，有哪些地方出现错误或者疑义，欢迎讨论，感谢阅读～

本文版权归作者所有，欢迎转载，转载请注明出处和链接来源。

