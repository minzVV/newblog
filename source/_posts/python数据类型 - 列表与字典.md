---
title: python数据类型 - 列表与字典
thumbnail: 
date: 2020-08-08 20:41:02
toc: true
tags:
    - python数据类型
categories:
    - [python,data_type]
---

> 摘要
python数据类型之列表(List)&字典(Dictionary),基础特性以及常用场景～
<!-- more -->


## 写在前面

上篇博文介绍了python中最基本的数据类型之一字符串，接下来介绍另外两个最常用的python数据类型：列表与字典，包括其特性与一些必须会的操作函数。在数据分析过程中，这两种数据结构也是用的比较多的，所以掌握这两种基础数据类型的操作是必不可少的～

## 列表(List)

> - 列表的表示： []
> - 列表的元素类型：可以是任意数据类型的python对象类型，包括数字、字符串、列表、布尔值..
> - 列表内的元素以**逗号**为分割，逗号与逗号之间即是一个整体
> - 列表是一种**有序**的集合，可以随时添加和删除其中的元素
> - 列表的索引从**0**开始

### 列表的高级特性

#### **索引**

> 列表的索引是从0开始的

```python
# 索引取值

li = [1,23,8,'giu',['时代','shkj',1737],True]
print(li[3])

# 输出

giu
```

> 可以根据索引对列表的元素进行修改与删除

```python
# 根据索引进行列表内元素修改

li[2] = 20
print(li)

#输出

[1, 23, 20, 'giu', ['时代', 'shkj', 1737], True]

# 根据索引列表内元素删除

del li[1]
print(li)

# 输出

[1, 20, 'giu', ['时代', 'shkj', 1737], True]
```
<br>

#### **切片**

> 切片的**输出**结果也是**list**

```python
# 切片取值

li = [1,23,8,'giu',['时代','shkj',1737],True]
print(li[2:5])

# 输出

[8, 'giu', ['时代', 'shkj', 1737]]
```

> 使用切片对列表的多个元素进行修改与删除

```python
# 根据切片进行列表内元素修改

li[1:3] = [11,32]
print(li)

# 输出

[1, 11, 32, 'giu', ['时代', 'shkj', 1737], True]

# 根据切片列表内元素删除

del li[2:5]
print(li)

# 输出

[1, 23, True]
```
<br>

#### **in 操作**

> 需要注意的是：列表内的元素以逗号为分割，即是一个整体，当元素是一个列表时，列表内的元素**不能单独**被in操作识别

```python
# in 操作

li = [12,'wudu',[83,'wudi',8]]
v = 11 in li
print(v)

# 输出

False

li = [12,'wudu',[83,'wudi',8]]
v = 'wudi' in li
print(v)

# 输出

False
```
<br>

### 常用方法

#### **append**

> append : **追加**，在原来的列表最后追加输入的参数(输入参数将作为**一个整体**追加)


```python
# append 

li = ['qwe','hsdj','sad','aw','12',12]
v = li.append('无敌')
print(li)

# 输出

['qwe', 'hsdj', 'sad', 'aw', '12', 12, '无敌']
```
<br>

#### **extend**

> **扩展**原列表，与append 的不同之处在于：append输入的参数作为**整体**追加到列表末尾，而extend输入的参数**内部for循环**，**逐个**加入列表

```python
li = ['qwe','hsdj','aw']
for i in [33,'sdh']:
     li.append(i)
print(li)

# 输出

['qwe', 'hsdj', 'aw', 33, 'sdh']

li.extend('nbeu')
print(li)

# 输出

['qwe', 'hsdj', 'aw', 33, 'sdh', 'n', 'b', 'e', 'u']
```
<br>

#### **insert**

>**插入**，在指定索引位置插入，第一个参数为索引位置，第二个参数为插入的值

```python
li = [11,22,33,23,32]
li.insert(2,668)
print(li)

# 输出

[11, 22, 668, 33, 23, 32]
```
<br>

#### **copy**

> 列表拷贝(复制)，属于**浅拷贝**


```python
# copy

li = ['qwe','hsdj','sad','aw','12',12]
v = li.copy()
print(v)

# 输出

['qwe', 'hsdj', 'sad', 'aw', '12', 12]
```
<br>

#### 列表 - **删除**

> 除了上面介绍的根据索引和切片对列表元素进行删除以外，还有以下三种方法：


```python
# pop

# 删除，默认状况下删除列表最后一个值，输入的参数为需要删除的索引位置


li = [11,22,33,23,32]
v = li.pop(3)
print(li)

# 特点是，能获取到所删除的值

print(v)

# 输出

[11, 22, 33, 32]
23

# remove

# 删除列表中的指定的值，左边优先


li = [11,22,33,23,32]
li.remove(22)
print(li)

# clear

# 清空列表

li = ['qwe','hsdj','sad','aw','12',12]
li.clear()
print(li)
```
<br>

#### **内置方法**


```python
# count

# 计算列表内元素出现的个数

li = ['qwe',12,'hsdj','aw','aw','12',12]
v = li.count(12)
print(v)

# index

# 根据值获取当前值的索引位置，由左往右，后置位参数为开始寻找的区间

li = [11,22,33,23,32,11,13,12]
v = li.index(11)
v1 = li.index(11,1,6)
print(v,v1)

# reverse

# 将当前列表进行反转

li = [11,22,33,23,32]
li.reverse()
print(li)

# sort

# 从小到大排序，reverse=True为从大到小排序

li = [11,22,33,23,32]
li.sort(reverse=True)
print(li)
```
<br>


## 字典(Dictionary)

### **必知**

> - 字典的表示：{}
> - 由**键值对**组成，以逗号分割，且是**无序**的，具有极快的**查找速度**
> - 列表、字典不能作为字典的key，字典的value可以是任何类型值
> - 字典是一种**可变容器**模型，且可存储任意类型对象，如字符串、数字、元组等其他容器模型
> - 字典中键是唯一的，如果重复最后的一个键值对会**替换**前面的，值不需要唯一

<br>

### **必会**

```python
# 创建字典

info = {
     1:'sad',
     'k1': ['sad',12,['sd','dw']],
     (1,22):'123'
 }
```
<br>

#### **获取键**

> 获取字典中键值对的**键**

```python
for item in info.keys():
     print(item)

# 输出

1
k1
(1, 22)
```
<br>

#### **获取值**

> 获取字典中键值对的**值**

```python
for item in info.values():
     print(item)

# 输出

sad
['sad', 12, ['sd', 'dw']]
123
```
<br>

#### **获取键与值**

> 同时获取键值对的**键与值**

```python
for k,v in info.items():
     print(k,v)
        
# 输出

1 sad
k1 ['sad', 12, ['sd', 'dw']]
(1, 22) 123
```
<br>

#### **get方法**

> 根据传入的键获得值，后置位参数为：如果键不存在时的**返回值**

```python
v = info.get('k',111)
print(v)

# 输出

111
```
<br>

#### 字典 - **更新**

> update ：**更新**字典内的键值对的两种方法

```python
# 字典形式传入

info.update({'k1':'1111','k3':'v3'})

# 赋值形式传入

info.update(k1=112,k5='d',k6=668)
print(dic)
```
<br>

#### **in操作**

```python
 dic = {
     'k1':'v1',
     'k2':'v2'
 }
 v = 'k1' in dic
 v1 = 'v1' in dic.values()
 print(v,v1)

# 输出

True True
```
<br>

#### **内置方法**

```python
# .pop

# 删除指定键的值，后置位参数为，如果指定键不存在的返回值

v = dic.pop('k111','不存在')
print(dic,v)

# .popitem

# 随机删除一个键值对，并获得该键值对，支持将键值对分别赋值给键和值

k,v = dic.popitem()
print(dic,k,v)

# .setdefault

# 根据传入的key设置值，若key已存在，不进行设置，并获取当前已存在的key的值

# 若key不存在，插入键值对，后置位参数默认为none，并获取当前生成的键值对的key的值

dic.setdefault('k3')
print(dic)

# .clear    删除字典

dic.clear()
print(dic)

# .copy    复制字典

v = dic.copy()
print(v)

# .fromkeys    根据传入的序列，创建字典，并指定统一的值

v = dict.fromkeys(['sadj',123,'999'],333)
print(v)

# dict.setdefault(key, default=None)    

# 和get()类似, 但如果键不已经存在于字典中，将会添加键并将值设为default
```
<br>

## 写在后面

本篇博客为学习数据分析时，学习python基本使用的笔记整理，很是浅显，不过学习是一件值得开心的事，不论所学是深或浅，千里之行始于足下嘛，只要在走，就不怕远～

本文版权归作者所有，欢迎转载，转载请注明出处和链接来源。