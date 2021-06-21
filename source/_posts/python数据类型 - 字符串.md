---
title: python数据类型 - 字符串
thumbnail: 
date: 2020-08-07 20:41:02
toc: true
tags:
    - python数据类型
categories:
    - [python,data_type]
---

> 摘要
python数据类型之字符串(String),基础特性以及常用场景～
<!-- more -->


## 前言

这篇文章主要介绍python在数据分析中的字符串的常用魔法,结合实例形式总结字符串常用魔法的概念、功能、使用方法以及相关注意事项。

## 常用方法

### **join**

> 字符串拼接：将字符串中的每一个元素按照**指定的分隔符**进行拼接

```python
test = '你是风儿我是沙'
v = ' '.join(test)

# 输出

你是风儿我是沙
你 是 风 儿 我 是 沙
```

<br>

字符串拼接拓展：

1. 连接少量字符串时，可以使用 **+** 号操作符 或者 **f-string**
2. 连接大量字符串时，推荐使用 **join** 和 **f-string**

<br>

### **find**

> 子序列查找：根据传入的参数寻找子序列，找到**第一个**后，获取其位置
>
> 参数：**sub** 指定要查找的子序列
>
> ​			**start**和**end** 指定寻找的区间

```python
test = 'hello world'
v = test.find('o',3,-1)
v1 = test.find('o',1,4)

# 输出
4
-1
```

通过上面的实例及输出，可以发现：

1. 在找到第一个符合传入的参数sub后，停止查找并获取sub在字符串中所在的位置(或者说下标)
2. 通过输出v和v1，得到的位置(下标)是在整个字符串中的位置，**而当返回为-1时，则为传入的指定区间的末尾，并非整个字符串的末尾**


<br>

### **split与rsplit**

> 字符串分割：以传入参数为分割符，默认将字符串全部分割
>
> split：默认**从左往右**进行分割；rsplit：指定**从右往左**进行分割
>
> 参数：**sep** 指定分割符
>
> ​			**maxsplit** 指定分割次数

```python
test = 'hello world'
v = test.split('l',2)
v1 = test.split('l',10)

# 输出

['he', '', 'o world']
['he', '', 'o wor', 'd']
```

通过输出对比，不难发现split需要注意的地方有：

1. 分割时，传入参数的匹配可视为**贪婪匹配**，如实例中v的输出为连续的 l 分割符视为一个整体进行前后分割
2. 结果输出并**不会**输出分割符
3. 分割次数若**大于**字符串中含有的分割符个数，不会报错，仅输出为最大分割次数所得到的结果

> 关于字符串分割拓展：
>
> 1. **partition**：以参数为分割符，且在拿到第一个参数时，将字符串分割成**3份**
> 2. **rpartition**：以参数为分割符，**由右向左**识别且在拿到第一个参数时，将字符串分割成3份
> 3. **splitlines**：只能根据**换行符**进行分割，传入的参数为**True**和**False**，结果为是否保留换行符显示

<br>

### **replace**

> **替换**：字符串中的指定子序列与传入参数进行**替换**
>
> 参数：**old**	指定被替换的子序列
>
> ​			**new**	传入替换的参数
>
> ​			**count**	替换个数(次数)，**默认为全部替换**

```python
test = 'hello world'
v = test.replace('hello','hi',2)

# 输出

hi world
```

<br>

#### **字符串替换的另一种方法**

> **maketrans**定义对应关系，**translate**进行替换

```python
s = str.maketrans('aeiou','12345')
v = 'osahodioashoioiwueuroiuapfhahweiuip'
result = v.translate(s)

# 输出

4s1h4d341sh4343w525r4351pfh1hw2353p
```

<br>

### **strip**

> 移除指定的字符串，并**优先最多匹配**，默认(即不传入参数时)情况下，去除左右空白以及 \t 、\n
>
> **lstrip**：指定**从左往右**进行匹配并移除
>
> **rstrip**：指定**从右往左**进行匹配并移除

```python
test = 'xaxexxaleaxaae'
v = test.strip('xa')
v1 = test.lstrip('xa')
v2 = test.rstrip('xae')

# 输出v

exxaleaxaae

# 输出v1

exxaleaxaa

# 输出v2

xaxexxal
```

strip使用总结：

1. 所谓优先最多匹配，指的是先传入参数为整体进行匹配，如果匹配不上再分解为单个字符或字符组合去进行匹配，如v2中传入的参数为xae，并指定从右往左进行匹配，匹配时优先顺序为 **xae — xa、ae — x、a、e**
2. strip是指从字符串的**左右两边**进行匹配移除，并**停止于**所谓匹配优先顺序中的所有情况都匹配不上

<br>

### **isupper和islower**

> **isupper**：判断字符串是否**全部**为大写
>
> **islower**：判断字符串是否**全部**为小写

```python
test = 'ASDDss'
v1 = test.isupper()
v2 = test.islower()

# 输出

False False
```

输出结果为**布尔值**，转换为大写用：**upper**；转换为小写用：**lower**

<br>

### **startswith和endswith**

> startswith：判断是否以传入的参数开头
>
> endswith：判断是否以传入的参数结尾

```python
test = 'djfkjahfkja'
v = test.startswith('dj')
v1 = test.endswith('aj')

# 输出v

True

# 输出v1

 False
```

输出结果为**布尔值**

<br>

## **其余方法**

### **判断型**

1. **isalnum**：判断字符串中是否只包含字母和数字（汉字也算）
2. **isalpha**：字符串中是否只包含字母（汉字也算）
3. **isdecimal**、**isdigit**、**isnumeric**
    字符串是否是数字，isdigit（包含一些**特殊字符**的数字）比isdecimal(**十进制小数**)牛逼点
    isnumeric(**包括汉字的数字也能识别**)👈👈👈👈👈三者之中最牛，但使用最多是isdecimal
4. **isidentifier**
    标识符：查看字符串是否由字母，数字，下划线组成，数字不能为首
5. **isprintable**：判断字符串中是否存在不可显示的字符，例如：\t 制表符   \n 换行符
6. **isspace**：判断是否全部是空格
7. **istitle**：判断是否为标题，标题的定义为每个字符首字母为大写；转换为标题用：title

<br>

### **center、ljust、rjust**

> 设置字符串的宽度，并使用字符填充
>
> **center**将内容居中，**ljust**将内容放左边，**rjust**将内容放右边
>
> 参数：**width**	指定宽度
>
> ​			**fillchar**	指定填充字符

```python
test = 'hello world'
v1 = test.center(21,'*')

# 输出

*****hello world*****
```

<br>

### **expandtabs**

> 断句：**匹配 \t** ，将字符串以6个为一组进行断句，并在匹配到 \t 的位置，**不够6个**时以空格补满
>
> 参数：**tabsize**	指定宽度	

```python
s = 'hfaushfhoahsfoih\tuisagfiu\tjdsba'
v = s.expandtabs(6)

# 输出

hfaushfhoahsfoih  uisagfiu    jdsba

# 实例应用

text = 'username\tage\tpassw\tsex\n张三\t12\t123\t男\n李四\t19\t123\t男\n陈陈\t18\t123\t女\n'
v = text.expandtabs(20)

# 输出结果

username            age                 passw               sex
张三                  12                  123                 男
李四                  19                  123                 男
陈陈                  18                  123                 女
```
<br>

### **swapcase**

> 大小写转换：与lower、upper不同之处在于，swapcase把字符串中**每一个**元素进行转换，而并非统一转换成大写或小写

```python
test = 'LAsjSi'
v = test.swapcase()

# 输出

laSJsI
```
<br>

## 总结

以上为数据分析中python字符串自带的一下常用方法，重温一遍并记录下来，以便查漏补缺，有哪些地方出现错误或者疑义，欢迎讨论，感谢阅读～



本文版权归作者所有，欢迎转载，转载请注明出处和链接来源。