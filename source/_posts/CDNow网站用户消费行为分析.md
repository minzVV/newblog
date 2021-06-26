---
title: 用户消费有哪些特征？用户质量又如何界定？
thumbnail: 
date: 2021-4-22 20:41:02
toc: true
tags:
    - 数据分析项目
categories:
    - [数据分析,项目实操]
---

> 摘要
借由在线音乐零售平台CDNow的部分用户消费数据，分析用户的消费特征、用户的消费质量等～
<!-- more -->


## **项目描述**

**项目名称**：CDNow在线音乐零售平台用户消费行为分析

**项目背景**：CDNow是一家网络公司，经营着一个在线购物网站，主要销售光盘和音乐相关产品。2000年7月，被Bertelsmann Music Group 以1.17亿美元收购；而后不久，由亚马逊承包经营。本文主要通过分析CDNow网站的用户购买明细数据来分析该网站的用户消费行为，使运营部门在营销时更具有针对性，从而节省成本，提高效率。

**字段说明**

> 用户ID：用户身份唯一标识(数据集中一个用户ID有多条消费记录)
>
> 购买日期：即消费日期
>
> 订单数：购买数量
>
> 订单金额：总消费金额

**项目目的**：

- 用户消费特征
  - 用户整体消费分析
  - 用户个人消费分析
  - 用户消费周期
- 用户分层
  - RFM模型
  - 用户活跃度分层
- 用户质量分析
  - 消费频次
  - 复购率
  - 回购率
  - 留存率

## **数据处理**

### 2.1 **数据加载**


```python
import pandas as pd
import numpy as np
import matplotlib.pyplot as plt
import seaborn as ss
import warnings
%matplotlib inline
```


```python
import os

os.chdir("/home/min/data")
warnings.filterwarnings("ignore")
```


```python
# 设置列名

colums = ['用户ID','购买日期','订单数','订单金额']
data = pd.read_csv("CDNOW.txt",names = colums,sep="\s+")
data.head()
```


<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
    
    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>用户ID</th>
      <th>购买日期</th>
      <th>订单数</th>
      <th>订单金额</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>0</td>
      <td>1</td>
      <td>19970101</td>
      <td>1</td>
      <td>11.77</td>
    </tr>
    <tr>
      <td>1</td>
      <td>2</td>
      <td>19970112</td>
      <td>1</td>
      <td>12.00</td>
    </tr>
    <tr>
      <td>2</td>
      <td>2</td>
      <td>19970112</td>
      <td>5</td>
      <td>77.00</td>
    </tr>
    <tr>
      <td>3</td>
      <td>3</td>
      <td>19970102</td>
      <td>2</td>
      <td>20.76</td>
    </tr>
    <tr>
      <td>4</td>
      <td>3</td>
      <td>19970330</td>
      <td>2</td>
      <td>20.76</td>
    </tr>
  </tbody>
</table>
</div>

</br>

### 2.2 **数据查看**


```python
data.describe()
```


<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
    
    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>用户ID</th>
      <th>购买日期</th>
      <th>订单数</th>
      <th>订单金额</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>count</td>
      <td>69659.000000</td>
      <td>6.965900e+04</td>
      <td>69659.000000</td>
      <td>69659.000000</td>
    </tr>
    <tr>
      <td>mean</td>
      <td>11470.854592</td>
      <td>1.997228e+07</td>
      <td>2.410040</td>
      <td>35.893648</td>
    </tr>
    <tr>
      <td>std</td>
      <td>6819.904848</td>
      <td>3.837735e+03</td>
      <td>2.333924</td>
      <td>36.281942</td>
    </tr>
    <tr>
      <td>min</td>
      <td>1.000000</td>
      <td>1.997010e+07</td>
      <td>1.000000</td>
      <td>0.000000</td>
    </tr>
    <tr>
      <td>25%</td>
      <td>5506.000000</td>
      <td>1.997022e+07</td>
      <td>1.000000</td>
      <td>14.490000</td>
    </tr>
    <tr>
      <td>50%</td>
      <td>11410.000000</td>
      <td>1.997042e+07</td>
      <td>2.000000</td>
      <td>25.980000</td>
    </tr>
    <tr>
      <td>75%</td>
      <td>17273.000000</td>
      <td>1.997111e+07</td>
      <td>3.000000</td>
      <td>43.700000</td>
    </tr>
    <tr>
      <td>max</td>
      <td>23570.000000</td>
      <td>1.998063e+07</td>
      <td>99.000000</td>
      <td>1286.010000</td>
    </tr>
  </tbody>
</table>
</div>




```python
data.info()
```

    <class 'pandas.core.frame.DataFrame'>
    RangeIndex: 69659 entries, 0 to 69658
    Data columns (total 4 columns):
    用户ID    69659 non-null int64
    购买日期    69659 non-null int64
    订单数     69659 non-null int64
    订单金额    69659 non-null float64
    dtypes: float64(1), int64(3)
    memory usage: 2.1 MB

</br>

### 2.3 **数据类型转换 & 添加字段**


```python
# 转换日期类型

data['购买日期'] = pd.to_datetime(data['购买日期'],format='%Y%m%d')
data['月份'] = data['购买日期'].values.astype('datetime64[M]')
```


```python
data.info()
```

    <class 'pandas.core.frame.DataFrame'>
    RangeIndex: 69659 entries, 0 to 69658
    Data columns (total 5 columns):
    用户ID    69659 non-null int64
    购买日期    69659 non-null datetime64[ns]
    订单数     69659 non-null int64
    订单金额    69659 non-null float64
    月份      69659 non-null datetime64[ns]
    dtypes: datetime64[ns](2), float64(1), int64(2)
    memory usage: 2.7 MB

</br>

```python
data.head()
```


<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
    
    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>用户ID</th>
      <th>购买日期</th>
      <th>订单数</th>
      <th>订单金额</th>
      <th>月份</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>0</td>
      <td>1</td>
      <td>1997-01-01</td>
      <td>1</td>
      <td>11.77</td>
      <td>1997-01-01</td>
    </tr>
    <tr>
      <td>1</td>
      <td>2</td>
      <td>1997-01-12</td>
      <td>1</td>
      <td>12.00</td>
      <td>1997-01-01</td>
    </tr>
    <tr>
      <td>2</td>
      <td>2</td>
      <td>1997-01-12</td>
      <td>5</td>
      <td>77.00</td>
      <td>1997-01-01</td>
    </tr>
    <tr>
      <td>3</td>
      <td>3</td>
      <td>1997-01-02</td>
      <td>2</td>
      <td>20.76</td>
      <td>1997-01-01</td>
    </tr>
    <tr>
      <td>4</td>
      <td>3</td>
      <td>1997-03-30</td>
      <td>2</td>
      <td>20.76</td>
      <td>1997-03-01</td>
    </tr>
  </tbody>
</table>
</div>

</br>

```python
# 检查空值

data.apply(lambda x:sum(x.isnull()))
```


    用户ID    0
    购买日期    0
    订单数     0
    订单金额    0
    月份      0
    dtype: int64

</br>

## **用户消费分析**

### 3.1 **用户总体消费趋势**


```python
# 按照月份初步查看用户总体消费趋势分析

# 风格选择

plt.style.use('ggplot')
plt.figure(figsize = (20,12))

# 每月的中销售额

plt.subplot(221)
data.groupby('月份')['订单金额'].sum().plot(fontsize = 24)
plt.title('总销售额',fontsize = 24)

# 每月的消费次数

plt.subplot(222)
data.groupby('月份')['购买日期'].count().plot(fontsize = 24)
plt.title('消费次数',fontsize = 24)

# 每月的销量

plt.subplot(223)
data.groupby('月份')['订单数'].sum().plot(fontsize = 24)
plt.title('总销量',fontsize = 24)

# 每月的消费人数

plt.subplot(224)
data.groupby('月份')['用户ID'].apply(lambda x:len(x.unique())).plot(fontsize = 24)
plt.title('消费人数',fontsize = 24)

plt.tight_layout()
plt.show()
```


![](https://gitee.com/minzvv/blog_images/raw/master/cdnow/output_15_0.png)

**图表解析**：四个折线图趋势基本一致，呈现为前3个月销量极高，销售额暴涨，往后骤然下降，最后趋于平稳。

- 消费金额在前三个月达到最高峰，后期消费金额较为平稳，呈小幅度下降趋势

- 前三个月消费次数在10000左右，往后月份消费次数基本维持在2500

- 产品购买量趋势呈现为早起购买量多 后期小幅度下降趋势

- 每月消费人数小于每月的消费次数（订单数），但是区别不大，前三个月每月的消费人数在8000-10000之间，后续月份平均2000左右，一样是前期消费人数多，后期平稳下降趋势

  出现这种状况，假设问题是出现在用户身上，早期时间段的用户有异常值，或者由于各类促销营销，由于只有消费数据，无法进一步进行判断

</br>

```python
# 数据透视查看 -- 按照月份查看用户购买金额、订单数、用户人数

data.pivot_table(index='月份',
                values=['订单数','订单金额','用户ID'],
                aggfunc={'订单数':'sum',
                        '订单金额':'sum',
                        '用户ID':'count'})
```

</br>

<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
    
    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>用户ID</th>
      <th>订单数</th>
      <th>订单金额</th>
    </tr>
    <tr>
      <th>月份</th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>1997-01-01</td>
      <td>8928</td>
      <td>19416</td>
      <td>299060.17</td>
    </tr>
    <tr>
      <td>1997-02-01</td>
      <td>11272</td>
      <td>24921</td>
      <td>379590.03</td>
    </tr>
    <tr>
      <td>1997-03-01</td>
      <td>11598</td>
      <td>26159</td>
      <td>393155.27</td>
    </tr>
    <tr>
      <td>1997-04-01</td>
      <td>3781</td>
      <td>9729</td>
      <td>142824.49</td>
    </tr>
    <tr>
      <td>1997-05-01</td>
      <td>2895</td>
      <td>7275</td>
      <td>107933.30</td>
    </tr>
    <tr>
      <td>1997-06-01</td>
      <td>3054</td>
      <td>7301</td>
      <td>108395.87</td>
    </tr>
    <tr>
      <td>1997-07-01</td>
      <td>2942</td>
      <td>8131</td>
      <td>122078.88</td>
    </tr>
    <tr>
      <td>1997-08-01</td>
      <td>2320</td>
      <td>5851</td>
      <td>88367.69</td>
    </tr>
    <tr>
      <td>1997-09-01</td>
      <td>2296</td>
      <td>5729</td>
      <td>81948.80</td>
    </tr>
    <tr>
      <td>1997-10-01</td>
      <td>2562</td>
      <td>6203</td>
      <td>89780.77</td>
    </tr>
    <tr>
      <td>1997-11-01</td>
      <td>2750</td>
      <td>7812</td>
      <td>115448.64</td>
    </tr>
    <tr>
      <td>1997-12-01</td>
      <td>2504</td>
      <td>6418</td>
      <td>95577.35</td>
    </tr>
    <tr>
      <td>1998-01-01</td>
      <td>2032</td>
      <td>5278</td>
      <td>76756.78</td>
    </tr>
    <tr>
      <td>1998-02-01</td>
      <td>2026</td>
      <td>5340</td>
      <td>77096.96</td>
    </tr>
    <tr>
      <td>1998-03-01</td>
      <td>2793</td>
      <td>7431</td>
      <td>108970.15</td>
    </tr>
    <tr>
      <td>1998-04-01</td>
      <td>1878</td>
      <td>4697</td>
      <td>66231.52</td>
    </tr>
    <tr>
      <td>1998-05-01</td>
      <td>1985</td>
      <td>4903</td>
      <td>70989.66</td>
    </tr>
    <tr>
      <td>1998-06-01</td>
      <td>2043</td>
      <td>5287</td>
      <td>76109.30</td>
    </tr>
  </tbody>
</table>
</div>

</br>

### 3.2 **用户个体消费分析**

#### 3.2.1 **用户消费金额 & 消费次数**


```python
# 用户个体消费

# 根据用户ID进行分组
# 1、用户消费金额 & 消费次数

group_user = data.groupby('用户ID').sum()
group_user.describe()
```

</br>

<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
    
    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>订单数</th>
      <th>订单金额</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>count</td>
      <td>23570.000000</td>
      <td>23570.000000</td>
    </tr>
    <tr>
      <td>mean</td>
      <td>7.122656</td>
      <td>106.080426</td>
    </tr>
    <tr>
      <td>std</td>
      <td>16.983531</td>
      <td>240.925195</td>
    </tr>
    <tr>
      <td>min</td>
      <td>1.000000</td>
      <td>0.000000</td>
    </tr>
    <tr>
      <td>25%</td>
      <td>1.000000</td>
      <td>19.970000</td>
    </tr>
    <tr>
      <td>50%</td>
      <td>3.000000</td>
      <td>43.395000</td>
    </tr>
    <tr>
      <td>75%</td>
      <td>7.000000</td>
      <td>106.475000</td>
    </tr>
    <tr>
      <td>max</td>
      <td>1033.000000</td>
      <td>13990.930000</td>
    </tr>
  </tbody>
</table>
</div>

**图表解析**：

- 用户平均购买7张CD，而标准差为17，说明波动较大；中位值只有3，更是说明了存在小部分用户购买了大数量的CD
- 用户平均消费金额106，中位值是43，也说明了存在极值干扰

</br>

#### 3.2.2 **用户消费金额 & 购买数量(散点图)**


```python
# 2、散点图 -- 设置去除某些极值

group_user.query('订单金额 < 4000').plot.scatter(x = '订单金额',y = '订单数',figsize = (20,10))
```

![](https://gitee.com/minzvv/blog_images/raw/master/cdnow/output_21_1.png)

**图表解析**：由于数据源是CD网站的销售数据，商品种类单一，金额和商品量呈线性，并不存在大量离群点。

</br>

```python
# 每笔订单消费金额

data.plot.scatter(x = '订单金额',y = '订单数',figsize = (20,10))
```

![](https://gitee.com/minzvv/blog_images/raw/master/cdnow/output_22_1.png)

**图表解析**：从订单角度分析，订单消费金额多数在0～400，而每笔订单购买数量大多在0～40，而且订单购买数量极值极少，基本与上方用户消费角度分析趋势一致。

</br>

#### 3.2.3 **消费金额分布**


```python
# 3、用户消费金额的分布图

group_user['订单金额'].plot.hist(bins = 20,figsize = (16,10))
```

![](https://gitee.com/minzvv/blog_images/raw/master/cdnow/output_25_1.png)

**图表解析**：用户大部分消费能力不高，基本上绝大部分用户处于很低的消费档次，图中基本看不到高消费用户。

</br>

#### 3.2.4 **用户消费次数**


```python
# 4、用户消费次数分布  --  去除某些极值

group_user.query('订单数 < 92')['订单数'].hist(bins = 40,figsize = (16,10))
```

![](https://gitee.com/minzvv/blog_images/raw/master/cdnow/output_27_1.png)

**图表解析**：排除部分极值干扰，可看出大部分用户购买CD的数量都在3张以内，购买大数量的用户属极少数。

</br>

#### 3.2.5 **用户累计消费金额占比**


```python
# 用户累计消费金额占比

user_cumsum = group_user.sort_values('订单金额').apply(lambda x:x.cumsum() / x.sum())
user_cumsum.reset_index()['订单金额'].plot(figsize = (16,10))
```

![](https://gitee.com/minzvv/blog_images/raw/master/cdnow/output_29_1.png)

**图表解析**：50%的用户仅贡献了15%的消费额，而排名前5000的用户就贡献了60%的消费金额。

</br>

```python
# user_cumsum.head(10)

# user_cumsum.tail(10)
```

</br>

### **用户消费周期**

#### 3.3.1 **用户首购时间**


```python
# 1、第一次购买时间

orderdt_min = data.groupby('用户ID').min()['购买日期']
orderdt_min.value_counts().plot(figsize = (16,10))
```

![](https://gitee.com/minzvv/blog_images/raw/master/cdnow/output_33_1.png)

**图表解析：**用户第一次购买基本集中在前三个月，而在2月份中下旬有一次较大的波动

</br>

#### 3.3.2 **用户最后一次购买时间**


```python
# 最后一次购买时间

orderdt_max = data.groupby('用户ID').max()['购买日期']
orderdt_max.value_counts().plot(figsize = (16,10))
```

![](https://gitee.com/minzvv/blog_images/raw/master/cdnow/output_35_1.png)

**图表解析：**

- 用户最后一次购买的分布比第一次购买分布要广
- 大部分最后一次购买在前三个月，说明很多用户购买一次之后就不再消费
- 往后月份，最后一次购买数呈小幅度递增，有上升趋势

</br>

## **用户分层**

### **RFM模型分层**

#### 4.1 **构建RFM模型**


```python
rfm = data.pivot_table(index = '用户ID',
                      values=['订单数','订单金额','购买日期'],
                      aggfunc={'购买日期':'max',
                              '订单金额':'sum',
                              '订单数':'sum'})
rfm.head()
```

</br>

<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
    
    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>订单数</th>
      <th>订单金额</th>
      <th>购买日期</th>
    </tr>
    <tr>
      <th>用户ID</th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>1</td>
      <td>1</td>
      <td>11.77</td>
      <td>1997-01-01</td>
    </tr>
    <tr>
      <td>2</td>
      <td>6</td>
      <td>89.00</td>
      <td>1997-01-12</td>
    </tr>
    <tr>
      <td>3</td>
      <td>16</td>
      <td>156.46</td>
      <td>1998-05-28</td>
    </tr>
    <tr>
      <td>4</td>
      <td>7</td>
      <td>100.50</td>
      <td>1997-12-12</td>
    </tr>
    <tr>
      <td>5</td>
      <td>29</td>
      <td>385.61</td>
      <td>1998-01-03</td>
    </tr>
  </tbody>
</table>
</div>

</br>

```python
-(rfm['购买日期'] - rfm['购买日期'].max()).head()
```


    用户ID
    1   545 days
    2   534 days
    3    33 days
    4   200 days
    5   178 days
    Name: 购买日期, dtype: timedelta64[ns]

</br>

```python
# 重命名 & 去除单位

rfm['R'] = -(rfm['购买日期'] - rfm['购买日期'].max()) / np.timedelta64(1,'D')
rfm.rename(columns={'订单数':'F','订单金额':'M'},inplace=True)
rfm.head()
```

</br>

<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
    
    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>F</th>
      <th>M</th>
      <th>购买日期</th>
      <th>R</th>
    </tr>
    <tr>
      <th>用户ID</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>1</td>
      <td>1</td>
      <td>11.77</td>
      <td>1997-01-01</td>
      <td>545.0</td>
    </tr>
    <tr>
      <td>2</td>
      <td>6</td>
      <td>89.00</td>
      <td>1997-01-12</td>
      <td>534.0</td>
    </tr>
    <tr>
      <td>3</td>
      <td>16</td>
      <td>156.46</td>
      <td>1998-05-28</td>
      <td>33.0</td>
    </tr>
    <tr>
      <td>4</td>
      <td>7</td>
      <td>100.50</td>
      <td>1997-12-12</td>
      <td>200.0</td>
    </tr>
    <tr>
      <td>5</td>
      <td>29</td>
      <td>385.61</td>
      <td>1998-01-03</td>
      <td>178.0</td>
    </tr>
  </tbody>
</table>
</div>

</br>

```python
rfm[['R','F','M']].apply(lambda x:x-x.mean()).head()
```

</br>


<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
    
    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>R</th>
      <th>F</th>
      <th>M</th>
    </tr>
    <tr>
      <th>用户ID</th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>1</td>
      <td>177.778362</td>
      <td>-6.122656</td>
      <td>-94.310426</td>
    </tr>
    <tr>
      <td>2</td>
      <td>166.778362</td>
      <td>-1.122656</td>
      <td>-17.080426</td>
    </tr>
    <tr>
      <td>3</td>
      <td>-334.221638</td>
      <td>8.877344</td>
      <td>50.379574</td>
    </tr>
    <tr>
      <td>4</td>
      <td>-167.221638</td>
      <td>-0.122656</td>
      <td>-5.580426</td>
    </tr>
    <tr>
      <td>5</td>
      <td>-189.221638</td>
      <td>21.877344</td>
      <td>279.529574</td>
    </tr>
  </tbody>
</table>
</div>

</br>

```python
def rfm_func(x):
    level = x.apply(lambda x:'1' if x>=0 else '0')
    label = level.R + level.F + level.M
    d = {
        '111':'重要价值客户',
        '011':'重要保持客户',
        '101':'重要挽留客户',
        '001':'重要发展客户',
        '110':'一般价值客户',
        '010':'一般保持客户',
        '100':'一般挽留客户',
        '000':'一般发展客户',        
    }
    result = d[label]
    return result

rfm['label'] = rfm[['R','F','M']].apply(lambda x:x-x.mean()).apply(rfm_func,axis = 1)
rfm.head()
```

</br>


<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
    
    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>F</th>
      <th>M</th>
      <th>购买日期</th>
      <th>R</th>
      <th>label</th>
    </tr>
    <tr>
      <th>用户ID</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>1</td>
      <td>1</td>
      <td>11.77</td>
      <td>1997-01-01</td>
      <td>545.0</td>
      <td>一般挽留客户</td>
    </tr>
    <tr>
      <td>2</td>
      <td>6</td>
      <td>89.00</td>
      <td>1997-01-12</td>
      <td>534.0</td>
      <td>一般挽留客户</td>
    </tr>
    <tr>
      <td>3</td>
      <td>16</td>
      <td>156.46</td>
      <td>1998-05-28</td>
      <td>33.0</td>
      <td>重要保持客户</td>
    </tr>
    <tr>
      <td>4</td>
      <td>7</td>
      <td>100.50</td>
      <td>1997-12-12</td>
      <td>200.0</td>
      <td>一般发展客户</td>
    </tr>
    <tr>
      <td>5</td>
      <td>29</td>
      <td>385.61</td>
      <td>1998-01-03</td>
      <td>178.0</td>
      <td>重要保持客户</td>
    </tr>
  </tbody>
</table>
</div>

</br>


```python
rfm.groupby('label').sum()
```

</br>

<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
    
    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>F</th>
      <th>M</th>
      <th>R</th>
    </tr>
    <tr>
      <th>label</th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>一般价值客户</td>
      <td>650</td>
      <td>7181.28</td>
      <td>36295.0</td>
    </tr>
    <tr>
      <td>一般保持客户</td>
      <td>1712</td>
      <td>19937.45</td>
      <td>29448.0</td>
    </tr>
    <tr>
      <td>一般发展客户</td>
      <td>13977</td>
      <td>196971.23</td>
      <td>591108.0</td>
    </tr>
    <tr>
      <td>一般挽留客户</td>
      <td>29346</td>
      <td>438291.81</td>
      <td>6951815.0</td>
    </tr>
    <tr>
      <td>重要价值客户</td>
      <td>11121</td>
      <td>167080.83</td>
      <td>358363.0</td>
    </tr>
    <tr>
      <td>重要保持客户</td>
      <td>107789</td>
      <td>1592039.62</td>
      <td>517267.0</td>
    </tr>
    <tr>
      <td>重要发展客户</td>
      <td>2023</td>
      <td>45785.01</td>
      <td>56636.0</td>
    </tr>
    <tr>
      <td>重要挽留客户</td>
      <td>1263</td>
      <td>33028.40</td>
      <td>114482.0</td>
    </tr>
  </tbody>
</table>
</div>

</br>

```python
rfm.groupby('label').count()
```

</br>

<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
    
    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>F</th>
      <th>M</th>
      <th>购买日期</th>
      <th>R</th>
    </tr>
    <tr>
      <th>label</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>一般价值客户</td>
      <td>77</td>
      <td>77</td>
      <td>77</td>
      <td>77</td>
    </tr>
    <tr>
      <td>一般保持客户</td>
      <td>206</td>
      <td>206</td>
      <td>206</td>
      <td>206</td>
    </tr>
    <tr>
      <td>一般发展客户</td>
      <td>3300</td>
      <td>3300</td>
      <td>3300</td>
      <td>3300</td>
    </tr>
    <tr>
      <td>一般挽留客户</td>
      <td>14074</td>
      <td>14074</td>
      <td>14074</td>
      <td>14074</td>
    </tr>
    <tr>
      <td>重要价值客户</td>
      <td>787</td>
      <td>787</td>
      <td>787</td>
      <td>787</td>
    </tr>
    <tr>
      <td>重要保持客户</td>
      <td>4554</td>
      <td>4554</td>
      <td>4554</td>
      <td>4554</td>
    </tr>
    <tr>
      <td>重要发展客户</td>
      <td>331</td>
      <td>331</td>
      <td>331</td>
      <td>331</td>
    </tr>
    <tr>
      <td>重要挽留客户</td>
      <td>241</td>
      <td>241</td>
      <td>241</td>
      <td>241</td>
    </tr>
  </tbody>
</table>
</div>

</br>

```python
# 各类型用户占比

user_t = rfm.groupby('label').count()
plt.axis('equal')
labels = ['一般价值客户','一般保持客户','一般发展客户','一般挽留客户','重要价值客户','重要保持客户','重要发展客户','重要挽留客户']
plt.pie(user_t['M'],
       autopct='%3.1f%%',
       labels=labels,
       pctdistance=0.9,
       labeldistance=1.2,
       radius = 3,
       startangle=15)
plt.show()
```


![](https://gitee.com/minzvv/blog_images/raw/master/cdnow/output_45_0.png)

</br>

```python
plt.figure(figsize=(16,10))
for label,grouped in rfm.groupby('label'):
    x = grouped['F']
    y = grouped['R']
    
    plt.scatter(x,y,label = label)
plt.legend(loc = 'best')
plt.xlabel('Frequency')
plt.ylabel('Recency')
plt.show()
```

![](https://gitee.com/minzvv/blog_images/raw/master/cdnow/output_46_0.png)

**图表解析**：可以看出，大部分用户为重要保持客户，原因可能是存在极值，也说明了RFM模型的划分应该依照业务的实际情况来进行划分层级。

</br>

### **活跃度分层**

#### 4.2 **按照活跃度分层用户**


```python
# 通过每月是否消费来划分用户

pivoted_counts = data.pivot_table(index='用户ID',
                                 columns='月份',
                                 values='购买日期',
                                 aggfunc='count').fillna(0)

pivoted_counts.columns = data['月份'].sort_values().astype('str').unique()
pivoted_counts.head()
```

</br>

<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
    
    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>1997-01-01</th>
      <th>1997-02-01</th>
      <th>1997-03-01</th>
      <th>1997-04-01</th>
      <th>1997-05-01</th>
      <th>1997-06-01</th>
      <th>1997-07-01</th>
      <th>1997-08-01</th>
      <th>1997-09-01</th>
      <th>1997-10-01</th>
      <th>1997-11-01</th>
      <th>1997-12-01</th>
      <th>1998-01-01</th>
      <th>1998-02-01</th>
      <th>1998-03-01</th>
      <th>1998-04-01</th>
      <th>1998-05-01</th>
      <th>1998-06-01</th>
    </tr>
    <tr>
      <th>用户ID</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>1</td>
      <td>1.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
    </tr>
    <tr>
      <td>2</td>
      <td>2.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
    </tr>
    <tr>
      <td>3</td>
      <td>1.0</td>
      <td>0.0</td>
      <td>1.0</td>
      <td>1.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>2.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>1.0</td>
      <td>0.0</td>
    </tr>
    <tr>
      <td>4</td>
      <td>2.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>1.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>1.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
    </tr>
    <tr>
      <td>5</td>
      <td>2.0</td>
      <td>1.0</td>
      <td>0.0</td>
      <td>1.0</td>
      <td>1.0</td>
      <td>1.0</td>
      <td>1.0</td>
      <td>0.0</td>
      <td>1.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>2.0</td>
      <td>1.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
    </tr>
  </tbody>
</table>
</div>

</br>

```python
data_purchase = pivoted_counts.applymap(lambda x:1 if x>0 else 0)
data_purchase.tail()
```

</br>

<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
    
    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>1997-01-01</th>
      <th>1997-02-01</th>
      <th>1997-03-01</th>
      <th>1997-04-01</th>
      <th>1997-05-01</th>
      <th>1997-06-01</th>
      <th>1997-07-01</th>
      <th>1997-08-01</th>
      <th>1997-09-01</th>
      <th>1997-10-01</th>
      <th>1997-11-01</th>
      <th>1997-12-01</th>
      <th>1998-01-01</th>
      <th>1998-02-01</th>
      <th>1998-03-01</th>
      <th>1998-04-01</th>
      <th>1998-05-01</th>
      <th>1998-06-01</th>
    </tr>
    <tr>
      <th>用户ID</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>23566</td>
      <td>0</td>
      <td>0</td>
      <td>1</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
    </tr>
    <tr>
      <td>23567</td>
      <td>0</td>
      <td>0</td>
      <td>1</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
    </tr>
    <tr>
      <td>23568</td>
      <td>0</td>
      <td>0</td>
      <td>1</td>
      <td>1</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
    </tr>
    <tr>
      <td>23569</td>
      <td>0</td>
      <td>0</td>
      <td>1</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
    </tr>
    <tr>
      <td>23570</td>
      <td>0</td>
      <td>0</td>
      <td>1</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
    </tr>
  </tbody>
</table>
</div>

</br>

```python
def active_status(data):
    status = []
    for i in range(18):
        if data[i] == 0:
            if len(status) > 0:
                if status[i-1] == 'unreg':
                    status.append('unreg')
                else:
                    status.append('unactive')
            else:
                status.append('unreg')
        else:
            if len(status) == 0:
                status.append('new')
            else:
                if status[i-1] == 'unactive':
                    status.append('return')
                elif status[i-1] == 'unreg':
                    status.append('new')
                else:
                    status.append('active')
    return pd.Series(status,data_purchase.columns)
```

</br>

```python
purchase_states = data_purchase.apply(active_status,axis = 1)
purchase_states.tail()
```

</br>

<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
    
    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>1997-01-01</th>
      <th>1997-02-01</th>
      <th>1997-03-01</th>
      <th>1997-04-01</th>
      <th>1997-05-01</th>
      <th>1997-06-01</th>
      <th>1997-07-01</th>
      <th>1997-08-01</th>
      <th>1997-09-01</th>
      <th>1997-10-01</th>
      <th>1997-11-01</th>
      <th>1997-12-01</th>
      <th>1998-01-01</th>
      <th>1998-02-01</th>
      <th>1998-03-01</th>
      <th>1998-04-01</th>
      <th>1998-05-01</th>
      <th>1998-06-01</th>
    </tr>
    <tr>
      <th>用户ID</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>23566</td>
      <td>unreg</td>
      <td>unreg</td>
      <td>new</td>
      <td>unactive</td>
      <td>unactive</td>
      <td>unactive</td>
      <td>unactive</td>
      <td>unactive</td>
      <td>unactive</td>
      <td>unactive</td>
      <td>unactive</td>
      <td>unactive</td>
      <td>unactive</td>
      <td>unactive</td>
      <td>unactive</td>
      <td>unactive</td>
      <td>unactive</td>
      <td>unactive</td>
    </tr>
    <tr>
      <td>23567</td>
      <td>unreg</td>
      <td>unreg</td>
      <td>new</td>
      <td>unactive</td>
      <td>unactive</td>
      <td>unactive</td>
      <td>unactive</td>
      <td>unactive</td>
      <td>unactive</td>
      <td>unactive</td>
      <td>unactive</td>
      <td>unactive</td>
      <td>unactive</td>
      <td>unactive</td>
      <td>unactive</td>
      <td>unactive</td>
      <td>unactive</td>
      <td>unactive</td>
    </tr>
    <tr>
      <td>23568</td>
      <td>unreg</td>
      <td>unreg</td>
      <td>new</td>
      <td>active</td>
      <td>unactive</td>
      <td>unactive</td>
      <td>unactive</td>
      <td>unactive</td>
      <td>unactive</td>
      <td>unactive</td>
      <td>unactive</td>
      <td>unactive</td>
      <td>unactive</td>
      <td>unactive</td>
      <td>unactive</td>
      <td>unactive</td>
      <td>unactive</td>
      <td>unactive</td>
    </tr>
    <tr>
      <td>23569</td>
      <td>unreg</td>
      <td>unreg</td>
      <td>new</td>
      <td>unactive</td>
      <td>unactive</td>
      <td>unactive</td>
      <td>unactive</td>
      <td>unactive</td>
      <td>unactive</td>
      <td>unactive</td>
      <td>unactive</td>
      <td>unactive</td>
      <td>unactive</td>
      <td>unactive</td>
      <td>unactive</td>
      <td>unactive</td>
      <td>unactive</td>
      <td>unactive</td>
    </tr>
    <tr>
      <td>23570</td>
      <td>unreg</td>
      <td>unreg</td>
      <td>new</td>
      <td>unactive</td>
      <td>unactive</td>
      <td>unactive</td>
      <td>unactive</td>
      <td>unactive</td>
      <td>unactive</td>
      <td>unactive</td>
      <td>unactive</td>
      <td>unactive</td>
      <td>unactive</td>
      <td>unactive</td>
      <td>unactive</td>
      <td>unactive</td>
      <td>unactive</td>
      <td>unactive</td>
    </tr>
  </tbody>
</table>
</div>

</br>

```python
# 去除未注册的用户 -- 设置为空值

purchase_states_m = purchase_states.replace('unreg',np.NaN).apply(lambda x:pd.value_counts(x))

# 转置

purchase_states_m = purchase_states_m.fillna(0).T
purchase_states_m
```

</br>

<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
    
    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>active</th>
      <th>new</th>
      <th>return</th>
      <th>unactive</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>1997-01-01</td>
      <td>0.0</td>
      <td>7846.0</td>
      <td>0.0</td>
      <td>0.0</td>
    </tr>
    <tr>
      <td>1997-02-01</td>
      <td>1157.0</td>
      <td>8476.0</td>
      <td>0.0</td>
      <td>6689.0</td>
    </tr>
    <tr>
      <td>1997-03-01</td>
      <td>1681.0</td>
      <td>7248.0</td>
      <td>595.0</td>
      <td>14046.0</td>
    </tr>
    <tr>
      <td>1997-04-01</td>
      <td>1773.0</td>
      <td>0.0</td>
      <td>1049.0</td>
      <td>20748.0</td>
    </tr>
    <tr>
      <td>1997-05-01</td>
      <td>852.0</td>
      <td>0.0</td>
      <td>1362.0</td>
      <td>21356.0</td>
    </tr>
    <tr>
      <td>1997-06-01</td>
      <td>747.0</td>
      <td>0.0</td>
      <td>1592.0</td>
      <td>21231.0</td>
    </tr>
    <tr>
      <td>1997-07-01</td>
      <td>746.0</td>
      <td>0.0</td>
      <td>1434.0</td>
      <td>21390.0</td>
    </tr>
    <tr>
      <td>1997-08-01</td>
      <td>604.0</td>
      <td>0.0</td>
      <td>1168.0</td>
      <td>21798.0</td>
    </tr>
    <tr>
      <td>1997-09-01</td>
      <td>528.0</td>
      <td>0.0</td>
      <td>1211.0</td>
      <td>21831.0</td>
    </tr>
    <tr>
      <td>1997-10-01</td>
      <td>532.0</td>
      <td>0.0</td>
      <td>1307.0</td>
      <td>21731.0</td>
    </tr>
    <tr>
      <td>1997-11-01</td>
      <td>624.0</td>
      <td>0.0</td>
      <td>1404.0</td>
      <td>21542.0</td>
    </tr>
    <tr>
      <td>1997-12-01</td>
      <td>632.0</td>
      <td>0.0</td>
      <td>1232.0</td>
      <td>21706.0</td>
    </tr>
    <tr>
      <td>1998-01-01</td>
      <td>512.0</td>
      <td>0.0</td>
      <td>1025.0</td>
      <td>22033.0</td>
    </tr>
    <tr>
      <td>1998-02-01</td>
      <td>472.0</td>
      <td>0.0</td>
      <td>1079.0</td>
      <td>22019.0</td>
    </tr>
    <tr>
      <td>1998-03-01</td>
      <td>571.0</td>
      <td>0.0</td>
      <td>1489.0</td>
      <td>21510.0</td>
    </tr>
    <tr>
      <td>1998-04-01</td>
      <td>518.0</td>
      <td>0.0</td>
      <td>919.0</td>
      <td>22133.0</td>
    </tr>
    <tr>
      <td>1998-05-01</td>
      <td>459.0</td>
      <td>0.0</td>
      <td>1029.0</td>
      <td>22082.0</td>
    </tr>
    <tr>
      <td>1998-06-01</td>
      <td>446.0</td>
      <td>0.0</td>
      <td>1060.0</td>
      <td>22064.0</td>
    </tr>
  </tbody>
</table>
</div>




```python
purchase_states_m.plot.area(figsize = (16,8))
plt.show()
```

![](https://gitee.com/minzvv/blog_images/raw/master/cdnow/output_53_0.png)

**图表解析**：

- 蓝色和灰色区域占了绝大部分面积，推断为某段时间消费过的用户的后续行为
- 红色部分代表的活跃用户较为稳定，属于核心用户
- 紫色部分代表的回流用户也表现的较为稳定

</br>

#### 4.3 **回流用户占比**

> 回流用户比：某个时间段内回流用户在总用户中的占比
>
> 回流用户率：相比上月有多少不活跃用户在本月进行了消费
>
> 活跃用户比：某个时间段内活跃用户在总用户中的占比


```python
plt.figure(figsize=(20,6))
rate = purchase_states_m.apply(lambda x: x/x.sum())
plt.plot(rate['return'],label = '回流用户')
plt.plot(rate['active'],label = '活跃用户')
plt.legend()
plt.show()
```

![](https://gitee.com/minzvv/blog_images/raw/master/cdnow/output_55_0.png)

**图表解析**：

- 用户每月回流用户比在5%~8%之间，且有小幅度下降趋势，说明客户有流失可能
- 数据源中不活跃用户量基本保持不变，所以回流率近似于回流比
- 活跃用户的占比基本稳定在3%~5%之间，下降趋势更明显
- 结合活跃用户和回流用户，在后期的消费用户中，近60%是回流用户，40%为活跃用户，说明整体用户质量相对不错。

</br>

## **用户质量分析**

### **用户购买周期**


```python
order_diff = data.groupby('用户ID').apply(lambda x: x['购买日期'] - x['购买日期'].shift())
order_diff.head(10)
```




    用户ID   
    1     0        NaT
    2     1        NaT
          2     0 days
    3     3        NaT
          4    87 days
          5     3 days
          6   227 days
          7    10 days
          8   184 days
    4     9        NaT
    Name: 购买日期, dtype: timedelta64[ns]

</br>

```python
order_diff.describe()
```



    count                      46089
    mean     68 days 23:22:13.567662
    std      91 days 00:47:33.924168
    min              0 days 00:00:00
    25%             10 days 00:00:00
    50%             31 days 00:00:00
    75%             89 days 00:00:00
    max            533 days 00:00:00
    Name: 购买日期, dtype: object

</br>

```python
# 订单周期分布

(order_diff / np.timedelta64(1,'D')).hist(bins = 20,figsize = (16,8))
plt.show()
```

![](https://gitee.com/minzvv/blog_images/raw/master/cdnow/output_59_0.png)

**图表解析**：

- 订单周期呈指数分布
- 用户的平均购买周期是68天，而且大多数用户的购买周期低于100天
- 从图像看属于典型的长尾图，说明绝大部分用户的消费间隔较短。可以考虑将时间召回点设为消费后立即赠送优惠券，且在消费后10天左右对客户进行回访，消费后30天后提醒优惠券即将到期，消费后60天短信推送进行尝试召回。

</br>

### **用户生命周期**


```python
user_life = data.groupby('用户ID')['购买日期'].agg(['min','max'])
user_life.head()
```

</br>

<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
    
    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>min</th>
      <th>max</th>
    </tr>
    <tr>
      <th>用户ID</th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>1</td>
      <td>1997-01-01</td>
      <td>1997-01-01</td>
    </tr>
    <tr>
      <td>2</td>
      <td>1997-01-12</td>
      <td>1997-01-12</td>
    </tr>
    <tr>
      <td>3</td>
      <td>1997-01-02</td>
      <td>1998-05-28</td>
    </tr>
    <tr>
      <td>4</td>
      <td>1997-01-01</td>
      <td>1997-12-12</td>
    </tr>
    <tr>
      <td>5</td>
      <td>1997-01-01</td>
      <td>1998-01-03</td>
    </tr>
  </tbody>
</table>
</div>

</br>

### **用户消费占比**

#### 5.1 **仅消费一次用户占比**


```python
# 只消费过一次的用户占比

user_once = (user_life['min'] == user_life['max']).value_counts()
labels = ['只消费一次用户','多次消费用户']
plt.axis('equal')
plt.pie(user_once,explode=(0,0.15),labels=labels,autopct='%2.1f%%',startangle=90,
       colors=('g','blue'),radius=1.5)
plt.show()
```


![](https://gitee.com/minzvv/blog_images/raw/master/cdnow/output_62_0.png)

</br>

```python
# 具体描述

(user_life['max'] - user_life['min']).describe()
```


    count                       23570
    mean     134 days 20:55:36.987696
    std      180 days 13:46:43.039788
    min               0 days 00:00:00
    25%               0 days 00:00:00
    50%               0 days 00:00:00
    75%             294 days 00:00:00
    max             544 days 00:00:00
    dtype: object

**图表解析**：

- 仅消费一次的用户占比为51.1%，进行多次消费用户占比48.9%.
- 而通过描述可知，用户的平均生命周期为134天，但中位数为0，说明大部分用户第一次消费也是最后一次，所以平均生命周期并不具有准确性。
- 用户生命周期最大值为544天，几乎等同于数据源总天数，代表这部分用户属于忠实拥护者，也是核心用户。

</br>

#### 5.2 **消费多次用户周期分布**


```python
# 多次消费用户的生命周期分布

plt.figure(figsize=(20,8))
plt.subplot(121)
((user_life['max'] - user_life['min']) / np.timedelta64(1,'D')).hist(bins = 15)
plt.title('二次消费以上用户的生命周期直方图')
plt.xlabel('天数')
plt.ylabel('用户数')


# 过滤生命周期为0的用户

plt.subplot(122)
user_twice = ((user_life['max'] - user_life['min']).reset_index()[0] / np.timedelta64(1,'D'))
user_twice[user_twice > 0].hist(bins = 30)
plt.title('二次消费以上用户的生命周期直方图')
plt.xlabel('天数')
plt.ylabel('用户数')

plt.show()
```


![](https://gitee.com/minzvv/blog_images/raw/master/cdnow/output_64_0.png)

**图表解析**：通过对比可知，去除周期为0的用户后，图像呈双峰结构。

- 右图为过滤后的生命周期分布图，但仍然存在生命周期趋于0天的用户，说明部分低质量用户，虽然消费过两次，但并不能持续消费。需要在消费后短时间内进行再次消费引导。
- 少部分用户集中在50~300天之间，忠诚度一般。
- 另一个高峰出现在400天之后，这部分用户属于高质量用户，忠诚度较高，而且后期用户数还在增加，需要维护这批用户的利益，长期保持回访、让利。

</br>

```python
# 消费两次以上的用户的平均生命周期

user_twice[user_twice > 0].mean()
```


    276.0448072247308

**图表解析**：消费两次以上的用户平均生命周期为276天，远高于总体平均生命周期。所以在用户首次消费后进行引导及召回可以很有效提高用户的生命周期。

</br>

### **复购率 & 回购率分析**

#### 6.1 **复购率**


```python
pivoted_counts.head()
```

</br>

<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
    
    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>1997-01-01</th>
      <th>1997-02-01</th>
      <th>1997-03-01</th>
      <th>1997-04-01</th>
      <th>1997-05-01</th>
      <th>1997-06-01</th>
      <th>1997-07-01</th>
      <th>1997-08-01</th>
      <th>1997-09-01</th>
      <th>1997-10-01</th>
      <th>1997-11-01</th>
      <th>1997-12-01</th>
      <th>1998-01-01</th>
      <th>1998-02-01</th>
      <th>1998-03-01</th>
      <th>1998-04-01</th>
      <th>1998-05-01</th>
      <th>1998-06-01</th>
    </tr>
    <tr>
      <th>用户ID</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>1</td>
      <td>1.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
    </tr>
    <tr>
      <td>2</td>
      <td>2.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
    </tr>
    <tr>
      <td>3</td>
      <td>1.0</td>
      <td>0.0</td>
      <td>1.0</td>
      <td>1.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>2.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>1.0</td>
      <td>0.0</td>
    </tr>
    <tr>
      <td>4</td>
      <td>2.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>1.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>1.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
    </tr>
    <tr>
      <td>5</td>
      <td>2.0</td>
      <td>1.0</td>
      <td>0.0</td>
      <td>1.0</td>
      <td>1.0</td>
      <td>1.0</td>
      <td>1.0</td>
      <td>0.0</td>
      <td>1.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>2.0</td>
      <td>1.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
    </tr>
  </tbody>
</table>
</div>

</br>

```python
# 复购率 -- 自然月内，购买多次的用户占比
# 消费两次及以上为1，消费一次为0，没有消费为空

purchare_r = pivoted_counts.applymap(lambda x: 1 if x > 1 else np.NaN if x == 0 else 0)
purchare_r.head()
```

</br>

<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
    
    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>1997-01-01</th>
      <th>1997-02-01</th>
      <th>1997-03-01</th>
      <th>1997-04-01</th>
      <th>1997-05-01</th>
      <th>1997-06-01</th>
      <th>1997-07-01</th>
      <th>1997-08-01</th>
      <th>1997-09-01</th>
      <th>1997-10-01</th>
      <th>1997-11-01</th>
      <th>1997-12-01</th>
      <th>1998-01-01</th>
      <th>1998-02-01</th>
      <th>1998-03-01</th>
      <th>1998-04-01</th>
      <th>1998-05-01</th>
      <th>1998-06-01</th>
    </tr>
    <tr>
      <th>用户ID</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>1</td>
      <td>0.0</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <td>2</td>
      <td>1.0</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <td>3</td>
      <td>0.0</td>
      <td>NaN</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>1.0</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>0.0</td>
      <td>NaN</td>
    </tr>
    <tr>
      <td>4</td>
      <td>1.0</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>0.0</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>0.0</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <td>5</td>
      <td>1.0</td>
      <td>0.0</td>
      <td>NaN</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>NaN</td>
      <td>0.0</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>1.0</td>
      <td>0.0</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
  </tbody>
</table>
</div>

</br>

```python
# 复购率折线图

(purchare_r.sum() / purchare_r.count()).plot(figsize = (16,8))
plt.show()
```

![](https://gitee.com/minzvv/blog_images/raw/master/cdnow/output_70_0.png)

**图表解析**：复购率稳定在20%~22%，而前三个月复购率低，可能是有大量新用户所导致。

</br>

#### 6.2 **回购率**

> 回购率 -- 曾经购买的用户在某一时期内的再次购买占比


```python
def purchare_back(data):
    status = []
    for i in range(17):
        # 本月进行过消费
        
        if data[i] == 1: 
            # 下个月是否进行了消费
            
            if data[i+1] == 1:
                # 消费为1 -- 回购了
                
                status.append(1)
            if data[i+1] == 0:
                # 消费为0 -- 没有回购
                
                status.append(0)
        else:
            # 之前没消费则不进行计算
            
            status.append(np.NaN)
    # 主要对最后一个月进行判断处理
    
    status.append(np.NaN)
    return pd.Series(status,data_purchase.columns)
```


```python
purchare_b = data_purchase.apply(purchare_back,axis = 1)
purchare_b.head()
```

</br>

<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
    
    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>1997-01-01</th>
      <th>1997-02-01</th>
      <th>1997-03-01</th>
      <th>1997-04-01</th>
      <th>1997-05-01</th>
      <th>1997-06-01</th>
      <th>1997-07-01</th>
      <th>1997-08-01</th>
      <th>1997-09-01</th>
      <th>1997-10-01</th>
      <th>1997-11-01</th>
      <th>1997-12-01</th>
      <th>1998-01-01</th>
      <th>1998-02-01</th>
      <th>1998-03-01</th>
      <th>1998-04-01</th>
      <th>1998-05-01</th>
      <th>1998-06-01</th>
    </tr>
    <tr>
      <th>用户ID</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>1</td>
      <td>0.0</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <td>2</td>
      <td>0.0</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <td>3</td>
      <td>0.0</td>
      <td>NaN</td>
      <td>1.0</td>
      <td>0.0</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>0.0</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>0.0</td>
      <td>NaN</td>
    </tr>
    <tr>
      <td>4</td>
      <td>0.0</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>0.0</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>0.0</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <td>5</td>
      <td>1.0</td>
      <td>0.0</td>
      <td>NaN</td>
      <td>1.0</td>
      <td>1.0</td>
      <td>1.0</td>
      <td>0.0</td>
      <td>NaN</td>
      <td>0.0</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>1.0</td>
      <td>0.0</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
  </tbody>
</table>
</div>

</br>

```python
# 折线图

plt.figure(figsize = (20,8))
plt.subplot(211)
(purchare_b.sum() / purchare_b.count()).plot()
plt.title('用户回购率分析')
plt.ylabel('百分比%')

plt.subplot(212)
plt.plot(purchare_b.sum(),label = '每月消费人数')
plt.plot(purchare_b.count(),label = '每月回购人数')
plt.xlabel('月份')
plt.ylabel('用户数')
plt.legend()

plt.show()
```

![](https://gitee.com/minzvv/blog_images/raw/master/cdnow/output_75_0.png)

**图表分析**：

- 回购率基本稳定在25%～35%，较高于复购率，但波动性较大。新用户回购率在15%左右，与老用户相差不大
- 从消费人数分布图可以发现，回购人数在前三个月后趋于稳定，所以波动的原因有可能是营销淡旺季所导致
- 结合回购率复购率，可以看出新用户的整体忠诚度低于老用户，而老用户回购率较高，消费频次较低。

</br>

### **留存率分析**


```python
user_purchare = data[['用户ID','订单数','订单金额','购买日期']]
user_purchare.head()
```

</br>

<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
    
    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>用户ID</th>
      <th>订单数</th>
      <th>订单金额</th>
      <th>购买日期</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>0</td>
      <td>1</td>
      <td>1</td>
      <td>11.77</td>
      <td>1997-01-01</td>
    </tr>
    <tr>
      <td>1</td>
      <td>2</td>
      <td>1</td>
      <td>12.00</td>
      <td>1997-01-12</td>
    </tr>
    <tr>
      <td>2</td>
      <td>2</td>
      <td>5</td>
      <td>77.00</td>
      <td>1997-01-12</td>
    </tr>
    <tr>
      <td>3</td>
      <td>3</td>
      <td>2</td>
      <td>20.76</td>
      <td>1997-01-02</td>
    </tr>
    <tr>
      <td>4</td>
      <td>3</td>
      <td>2</td>
      <td>20.76</td>
      <td>1997-03-30</td>
    </tr>
  </tbody>
</table>
</div>

</br>

```python
user_purchare_retention = pd.merge(left = user_purchare,
                                  right = user_life['min'].reset_index(),
                                  how = 'inner',
                                  on = '用户ID')
user_purchare_retention['order_dt_diff'] = user_purchare_retention['购买日期'] - user_purchare_retention['min']
user_purchare_retention['dt_diff'] = user_purchare_retention.order_dt_diff.apply(lambda x: x / np.timedelta64(1,'D'))
user_purchare_retention.head()
```

</br>

<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
    
    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>用户ID</th>
      <th>订单数</th>
      <th>订单金额</th>
      <th>购买日期</th>
      <th>min</th>
      <th>order_dt_diff</th>
      <th>dt_diff</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>0</td>
      <td>1</td>
      <td>1</td>
      <td>11.77</td>
      <td>1997-01-01</td>
      <td>1997-01-01</td>
      <td>0 days</td>
      <td>0.0</td>
    </tr>
    <tr>
      <td>1</td>
      <td>2</td>
      <td>1</td>
      <td>12.00</td>
      <td>1997-01-12</td>
      <td>1997-01-12</td>
      <td>0 days</td>
      <td>0.0</td>
    </tr>
    <tr>
      <td>2</td>
      <td>2</td>
      <td>5</td>
      <td>77.00</td>
      <td>1997-01-12</td>
      <td>1997-01-12</td>
      <td>0 days</td>
      <td>0.0</td>
    </tr>
    <tr>
      <td>3</td>
      <td>3</td>
      <td>2</td>
      <td>20.76</td>
      <td>1997-01-02</td>
      <td>1997-01-02</td>
      <td>0 days</td>
      <td>0.0</td>
    </tr>
    <tr>
      <td>4</td>
      <td>3</td>
      <td>2</td>
      <td>20.76</td>
      <td>1997-03-30</td>
      <td>1997-01-02</td>
      <td>87 days</td>
      <td>87.0</td>
    </tr>
  </tbody>
</table>
</div>

</br>

```python
# 区间分组

bin = [0,30,60,90,120,150,180,365]
user_purchare_retention['date_diff_bin'] = pd.cut(user_purchare_retention.dt_diff,bins = bin)
user_purchare_retention.head(10)
```

</br>

<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
    
    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>用户ID</th>
      <th>订单数</th>
      <th>订单金额</th>
      <th>购买日期</th>
      <th>min</th>
      <th>order_dt_diff</th>
      <th>dt_diff</th>
      <th>date_diff_bin</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>0</td>
      <td>1</td>
      <td>1</td>
      <td>11.77</td>
      <td>1997-01-01</td>
      <td>1997-01-01</td>
      <td>0 days</td>
      <td>0.0</td>
      <td>NaN</td>
    </tr>
    <tr>
      <td>1</td>
      <td>2</td>
      <td>1</td>
      <td>12.00</td>
      <td>1997-01-12</td>
      <td>1997-01-12</td>
      <td>0 days</td>
      <td>0.0</td>
      <td>NaN</td>
    </tr>
    <tr>
      <td>2</td>
      <td>2</td>
      <td>5</td>
      <td>77.00</td>
      <td>1997-01-12</td>
      <td>1997-01-12</td>
      <td>0 days</td>
      <td>0.0</td>
      <td>NaN</td>
    </tr>
    <tr>
      <td>3</td>
      <td>3</td>
      <td>2</td>
      <td>20.76</td>
      <td>1997-01-02</td>
      <td>1997-01-02</td>
      <td>0 days</td>
      <td>0.0</td>
      <td>NaN</td>
    </tr>
    <tr>
      <td>4</td>
      <td>3</td>
      <td>2</td>
      <td>20.76</td>
      <td>1997-03-30</td>
      <td>1997-01-02</td>
      <td>87 days</td>
      <td>87.0</td>
      <td>(60.0, 90.0]</td>
    </tr>
    <tr>
      <td>5</td>
      <td>3</td>
      <td>2</td>
      <td>19.54</td>
      <td>1997-04-02</td>
      <td>1997-01-02</td>
      <td>90 days</td>
      <td>90.0</td>
      <td>(60.0, 90.0]</td>
    </tr>
    <tr>
      <td>6</td>
      <td>3</td>
      <td>5</td>
      <td>57.45</td>
      <td>1997-11-15</td>
      <td>1997-01-02</td>
      <td>317 days</td>
      <td>317.0</td>
      <td>(180.0, 365.0]</td>
    </tr>
    <tr>
      <td>7</td>
      <td>3</td>
      <td>4</td>
      <td>20.96</td>
      <td>1997-11-25</td>
      <td>1997-01-02</td>
      <td>327 days</td>
      <td>327.0</td>
      <td>(180.0, 365.0]</td>
    </tr>
    <tr>
      <td>8</td>
      <td>3</td>
      <td>1</td>
      <td>16.99</td>
      <td>1998-05-28</td>
      <td>1997-01-02</td>
      <td>511 days</td>
      <td>511.0</td>
      <td>NaN</td>
    </tr>
    <tr>
      <td>9</td>
      <td>4</td>
      <td>2</td>
      <td>29.33</td>
      <td>1997-01-01</td>
      <td>1997-01-01</td>
      <td>0 days</td>
      <td>0.0</td>
      <td>NaN</td>
    </tr>
  </tbody>
</table>
</div>

</br>

```python
pivoted_retention = user_purchare_retention.pivot_table(index='用户ID',columns='date_diff_bin',values='订单金额',aggfunc=sum)
pivoted_retention.head()
```

</br>

<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
    
    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th>date_diff_bin</th>
      <th>(0, 30]</th>
      <th>(30, 60]</th>
      <th>(60, 90]</th>
      <th>(90, 120]</th>
      <th>(120, 150]</th>
      <th>(150, 180]</th>
      <th>(180, 365]</th>
    </tr>
    <tr>
      <th>用户ID</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>3</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>40.3</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>78.41</td>
    </tr>
    <tr>
      <td>4</td>
      <td>29.73</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>41.44</td>
    </tr>
    <tr>
      <td>5</td>
      <td>13.97</td>
      <td>38.90</td>
      <td>NaN</td>
      <td>45.55</td>
      <td>38.71</td>
      <td>26.14</td>
      <td>155.54</td>
    </tr>
    <tr>
      <td>7</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>97.43</td>
    </tr>
    <tr>
      <td>8</td>
      <td>NaN</td>
      <td>13.97</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>45.29</td>
      <td>104.17</td>
    </tr>
  </tbody>
</table>
</div>

</br>

```python
pivoted_retention.mean()
```


    date_diff_bin
    (0, 30]       51.540649
    (30, 60]      50.215070
    (60, 90]      48.975277
    (90, 120]     48.649005
    (120, 150]    51.399450
    (150, 180]    49.932592
    (180, 365]    91.960059
    dtype: float64

</br>

```python
pivoted_retention_trans = pivoted_retention.fillna(0).applymap(lambda x: 1 if x>0 else 0)
pivoted_retention_trans.head()
```

</br>

<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
    
    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th>date_diff_bin</th>
      <th>(0, 30]</th>
      <th>(30, 60]</th>
      <th>(60, 90]</th>
      <th>(90, 120]</th>
      <th>(120, 150]</th>
      <th>(150, 180]</th>
      <th>(180, 365]</th>
    </tr>
    <tr>
      <th>用户ID</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>3</td>
      <td>0</td>
      <td>0</td>
      <td>1</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>1</td>
    </tr>
    <tr>
      <td>4</td>
      <td>1</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>1</td>
    </tr>
    <tr>
      <td>5</td>
      <td>1</td>
      <td>1</td>
      <td>0</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
    </tr>
    <tr>
      <td>7</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>1</td>
    </tr>
    <tr>
      <td>8</td>
      <td>0</td>
      <td>1</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>1</td>
      <td>1</td>
    </tr>
  </tbody>
</table>
</div>

</br>

```python
((pivoted_retention_trans.sum() / pivoted_retention_trans.count()) * 100).plot.bar(figsize = (16,10))
plt.ylabel('百分比%')
plt.xticks(fontsize = 16,rotation = 360)
plt.title('各时间区间的用户留存率')
plt.show()
```

![](https://gitee.com/minzvv/blog_images/raw/master/cdnow/output_83_0.png)



**图表解析**：

- 第一个月的留存率达到了38%，历经两个月下滑，之后趋于稳定。说明用户在通过前三个月的使用，逐渐出现分歧，即部分用户成为忠实用户，剩余部分用户流失。有20%左右的用户在第一次购买后的三个月到半年之间有过再次消费，27%的用户在半年后至一年内进行了再次购买。
- 相比于拉新而言，应该更注重用户忠诚度的培养。如果进行活动，应该放在前三个月。
- 结合用户生命周期，用户的平均消费时间间隔为68天，所以应该在60天左右的间隔对用户进行召回和引导。

</br>

## **写在最后**

以上为CDNow网站用户消费行为分析的全部内容，有错误之处，恳请指正。感谢阅读！