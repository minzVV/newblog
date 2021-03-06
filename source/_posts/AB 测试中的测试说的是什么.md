---
title: A/B 测试的概念认识
thumbnail: 
date: 2021-5-15 22:41:02
toc: true
tags:
    - A/B-test
categories:
    - [数据分析,名词解析]
---

> 摘要
深入了解什么是数据指标？以及数据指标的作用和分类～
<!-- more -->
## A/B 测试中的测试说的是什么
</br>

## **测试的重要性**

> 测试是数据分析中的最常用的一种手段

早期选定的目标往往是尝试性的，但并不是一成不变的。因为早期设定的目标和关键数据指标一般都是基于假设，这就导致与用户或者**现实中的真实行为**之间有着**差别**，或细微，或显著。所以，及时调**整目标和关键数据指标**都是**可行的**，也是**必要的**，而调整所需要的依据(数据支撑)所用的方式一般都通过**测试**来得到。

</br>

## **测试的概念**

> 通常来说，测试就是通过市场细分、同期群分析或A/B测试来比较**两个样本**的不同。

</br>

## **常用测试手段**
</br>

### **市场细分**

- 概念：细分市场就是一群拥有某种**共同特征**的人。
- 举例：
  - 了解产品对哪类家庭更具吸引力就是一种市场细分，可以反馈出一开始设定的划分活跃/非活跃用户的准绳实际上并不能很好的反映实际用户的参与度。
  - 去餐馆前是否先预约也是一种市场细分，而根据后续的消费、评价等各个数据指标进行分析得出区别，从而进行不同的维护联系
- 适用场景
  - 可应用于网站、或者其他行业、各种形式的营销
</br>

### **同期群分析**

- 概念：比较相似群体随**时间**的变化。能够观察处于生命周期不同阶段客户的行为模式，而非一刀切所有用户。
- 举例：
  - 在产品上线第一个月就"吃螃蟹"的用户与四个月后才加入的用户肯定有不同的上手体验，寻求这对用户流失率有什么影响，就是一种常用的同期群分析的使用场景
- 适用场景
  - 营收、客户流失率、口碑的病毒式传播、客户支持成本等数据指标
</br>

#### **同期群分析实例**

场景设置：一家网店，每个月能获取1000个新客户，他们每个人都会买一些东西。下表显示了前五个月中平均每位客户带来的营收。

表2-1：5个月平均每位客户营收

|                  | 1月   | 2月     | 3月      | 4月      | 5月     |
| ---------------- | ----- | ------- | -------- | -------- | ------- |
| 客户总数         | 1000  | 2000    | 3000     | 4000     | 5000    |
| 平均每位客户营收 | 5美元 | 4.5美元 | 4.33美元 | 4.25美元 | 4.5美元 |

由表2-1传达的信息：营收在经历小幅下降后又回升，而评价每位客户带来的营收值**几近均一**；而网店的生意到底是好是坏？无法回答，原因是没有对比新客户和老客户的表现；而且最主要的是表2-1中将新客户和5个月前注册的老客户的数据是**混淆**在一起的。

使用相同的数据，根据客户首次光顾的时间按**月份**进行分段。

</br>

表2-2：按照客户首次光顾月份比较营收

|          | 1月   | 2月   | 3月   | 4月   | 5月     |
| -------- | ----- | ----- | ----- | ----- | ------- |
| 新客户数 | 1000  | 1000  | 1000  | 1000  | 1000    |
| 总客户数 | 1000  | 2000  | 3000  | 4000  | 5000    |
| 第一个月 | 5美元 | 3美元 | 2美元 | 1美元 | 0.5美元 |
| 第二个月 |       | 6美元 | 4美元 | 2美元 | 1美元   |
| 第三个月 |       |       | 7美元 | 6美元 | 5美元   |
| 第四个月 |       |       |       | 8美元 | 7美元   |
| 第五个月 |       |       |       |       | 9美元   |

从表2-2中，在第五个月光顾的网店客户，其首月平均消费为9美元，相比较第一个月光顾的客户消费额翻了近1倍，这是一个巨大的增长，而不是像表2-1中显示的几近均一。

根据用户在网店上的**"店龄"**来划分数据，则显示了另一个重要的数据指标。

</br>

表2-3：营收数据的同期群分析

|        |       |       | 使用月份 |       |         |
| ------ | ----- | ----- | -------- | ----- | ------- |
|        | 1     | 2     | 3        | 4     | 5       |
| 1月    | 5美元 | 3美元 | 2美元    | 1美元 | 0.5美元 |
| 2月    | 6美元 | 4美元 | 2没有    | 1美元 |         |
| 3月    | 7美元 | 6美元 | 5美元    |       |         |
| 4月    | 8美元 | 7美元 |          |       |         |
| 5月    | 9美元 |       |          |       |         |
|        |       |       |          |       |         |
| 平均值 | 7美元 | 5美元 | 3美元    | 1美元 | 0.5美元 |

如表2-3所示，1月份的同期群首月消费了5美元，然后逐月递减，**到第五个月仅消费0.5美元**；往后月份也**有所递减**，可以说前几个月中用户变现的疲软表现已经对网店营收指标的总体状况造成损害，但后续随着网站的发展，**新客户的首月消费**有显著的增长，所以呈现出来表2-1的平滑现象。但数据显示，网店其实在茁壮成长，随之而来引起重视的数据指标将是：在首月注册消费后，**客户消费的递减量**。

</br>

#### **小结**

通过这个实例，具体的展现了同期群分析的实用性。通过同期群分析能够观察处于生命周期不同阶段客户的行为模式，而非忽略个体的自然生命周期，对所有客户一次性切分；而通过比较不同的同期群，可以获知：从总体上看，关键指标的表现是否越来越好。

</br>

### **A/B和多变量测试**
</br>

#### **A/B测试**

- 概念：假设**其他条件保持不变**，仅考虑某一属性对用户的影响，就是所谓的A/B测试、
- 举例：
  - 对半数用户展示一个绿色链接，对另一半用户展示蓝色链接；观察哪种颜色的链接点击率更高
  - 触发访客行为：立即试用产品链接的文字设置为 "免费试用"/"免费开始"
- 缺点：A/B测试需要大量的数据反馈，在**用户流量巨大**的大型网站上进行A/B测试能短时间得到答案，而在一些流量相对较小的网站上需要一定的时间成本，同时也可能需要测试多个因素，例如网页的色调、触发用户行为的链接文字、图片效果等

</br>

#### **多变量测试**

- 概念：用**统计学方法**剥离出单个影响因子与结果中某一项指标提升的相关性。
- 举例：
  - 同时改动产品的多个方面，看看哪个与结果的相关性最大
- 适用场景
  - 在一些用户流量没那么大的应用场景下，相比较A/B测试的单一属性测试，多变量分析能更快得到结果和数据反馈

</br>

## **总结**

测试是数据分析的灵魂。市场细分是对按某属性分类的人群进行横向比较，而同期群分析是沿着客户群体的自然生命周期收集并比较相似群体；而A/B测试和多变量测试均是研究在同一时间段内对不同群体提供不同的体验，从而得到哪种体验更得人心。

</br>

参考文章:  

[书籍 - 精益数据分析]













