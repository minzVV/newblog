---
title: MySQL练习 - 基础篇(持续更新ing)
thumbnail: 
date: 2021-10-3 22:33:36
toc: true
tags:
    - MySQL练习题
categories:
    - [SQL,MySQL]
---

> 摘要
MySQL练习题，一些常用的基础知识点的练习..
<!-- more -->

## **MySQL - 简易篇**
</br>

### **重复值查找**
</br>

**[题目]**

编写一个SQL查询，查找学生表中所有重复的学生名。

```mysql
# 建表

create table exerciseOne(
	id int not null auto_increment,
	name varchar(64) not null,
	primary key(id));

desc exerciseOne;
+-------+-------------+------+-----+---------+----------------+
| Field | Type        | Null | Key | Default | Extra          |
+-------+-------------+------+-----+---------+----------------+
| id    | int(11)     | NO   | PRI | NULL    | auto_increment |
| name  | varchar(64) | NO   |     | NULL    |                |
+-------+-------------+------+-----+---------+----------------+

# 插入数据

insert into exerciseOne(id,name) values (001,'zhangsan'),(002,'ali'),(003,'luox'),(004,'luox'),(005,'ali');

# 数据查看

select * from exerciseOne;
+----+----------+
| id | name     |
+----+----------+
|  1 | zhangsan |
|  2 | ali      |
|  3 | luox     |
|  4 | luox     |
|  5 | ali      |
+----+----------+

```
</br>

**[思路]**

- 读题：查找学生表中所有**重复**的**学生名**
- 解题：以**学生名**分组，筛选出各个分组**计数大于1**的**学生名**



```mysql

select name from exerciseOne group by name having count(name) > 1;

+------+
| name |
+------+
| ali  |
| luox |
+------+
```

</br>

### 寻找第N高的数据

</br>

**[题目]**

exercise2 表中，记录了学生选修课程的名称以及成绩

找出语文课中成绩第二高的学生成绩，如果不存在第二高成绩的学生，那么查询应返回 null 。

```mysql
# 建表

create table exercise2(
    id int not null comment '学号',
    course varchar(64) not null comment '课程',
    score int comment '成绩');

# 查看
desc exercise2;

+--------+-------------+------+-----+---------+-------+
| Field  | Type        | Null | Key | Default | Extra |
+--------+-------------+------+-----+---------+-------+
| id     | int(11)     | NO   |     | NULL    |       |
| course | varchar(64) | NO   |     | NULL    |       |
| score  | int(11)     | YES  |     | NULL    |       |
+--------+-------------+------+-----+---------+-------+

# 插入数据
insert into exercise2(id,course,score)
values
(1,"语文",90),(1,"数学",65),(2,"语文",68),(2,"数学",96),(3,"数学",55);

# 查看数据

select * from exercise2;

+----+--------+-------+
| id | course | score |
+----+--------+-------+
|  1 | 语文   |    90 |
|  1 | 数学   |    65 |
|  2 | 语文   |    68 |
|  2 | 数学   |    96 |
|  3 | 数学   |    55 |
+----+--------+-------+

```

</br>

**[思路]**

- 读题：找出**语文课**中成绩**第二高**的学生成绩，如果**不存在第二高成绩**的学生，那么查询应**返回 null** 。
- 解题
  - 找出所有选修了"语文"课的学生成绩
  - 进行排序并选择第二高成绩
  - 特殊情况：如果不存在第二高的成绩，返回null

```mysql

select ifnull((
	select distinct score from exercise2 where course = "语文"
    order by score desc
    limit 1,1
),null) as "语文第二高成绩" ;

+-----------------------+
| 语文第二高成绩        |
+-----------------------+
|                    68 |
+-----------------------+
```



```mysql
# 验证特殊情况

insert into exercise2(id,course,score) values (1,"英语",77),(2,"英语",77);

+----+--------+-------+
| id | course | score |
+----+--------+-------+
|  1 | 语文   |    90 |
|  1 | 数学   |    65 |
|  2 | 语文   |    68 |
|  2 | 数学   |    96 |
|  3 | 数学   |    55 |
|  1 | 英语   |    77 |
|  2 | 英语   |    77 |
+----+--------+-------+

select ifnull((
	select distinct score from exercise2 where course = "英语"
    order by score desc
    limit 1,1
),null) as "英语第二高成绩" ;

+-----------------------+
| 英语第二高成绩        |
+-----------------------+
|                  NULL |
+-----------------------+
```

</br>

### **多表联接**

</br>

**[题目]**

现在有两个表：

- **学生表**：记录了学生的基本信息，字段有 **"学号"**、**"姓名"**
- **成绩表**：记录了学生选修的**课程**，以及对于课程的**成绩**，字段对应为 **"课程"**、**"成绩"**
- 两张表通过 **"学号"** 进行关联

现在需要查找出所有的学生的学号、姓名、课程和成绩。



```mysql
# 建学生表

create table student_lx3(
	id int not null comment "学号",
	name varchar(64) not null comment "姓名");

# 查看
desc student_lx3;

+-------+-------------+------+-----+---------+-------+
| Field | Type        | Null | Key | Default | Extra |
+-------+-------------+------+-----+---------+-------+
| id    | int(11)     | NO   |     | NULL    |       |
| name  | varchar(64) | NO   |     | NULL    |       |
+-------+-------------+------+-----+---------+-------+

# 插入数据
insert into student_lx3(id,name)
values 
(1,"张三"),(2,"赵四"),(3,"李五"),(4,"王六");

# 查看数据

select * from student_lx3;

+----+--------+
| id | name   |
+----+--------+
|  1 | 张三   |
|  2 | 赵四   |
|  3 | 李五   |
|  4 | 王六   |
+----+--------+


#建成绩表

create table score_lx3(
    id int not null comment "学号",
    course varchar(64) comment "课程",
    score int comment "成绩");

# 查看
desc score_lx3;

+--------+-------------+------+-----+---------+-------+
| Field  | Type        | Null | Key | Default | Extra |
+--------+-------------+------+-----+---------+-------+
| id     | int(11)     | NO   |     | NULL    |       |
| course | varchar(64) | YES  |     | NULL    |       |
| score  | int(11)     | YES  |     | NULL    |       |
+--------+-------------+------+-----+---------+-------+

# 插入数据

insert into score_lx3(id,course,score) 
values 
(1,"语文",90),(1,"数学",67),(2,"语文",88),(3,"数学",99),(3,"英语",77);

# 查看数据

select * from score_lx3;

+----+--------+-------+
| id | course | score |
+----+--------+-------+
|  1 | 语文   |    90 |
|  1 | 数学   |    67 |
|  2 | 语文   |    88 |
|  3 | 数学   |    99 |
|  3 | 英语   |    77 |
+----+--------+-------+

# 为保证数据的有效性和完整性，添加约束(外键约束)，在多表的一方添加外键约束
# 添加了外键约束之后的特征：
# - 主表中不能删除从表中已引用的数据
# - 从表中不能添加主表中不存在的数据

```

</br>

**[思路]**

- 读题：查找所有学生的**学号**、**姓名**、**课程**和**成绩**。
- 解题：
  - 其中 **"学号"** & **"姓名"** 在**student_lx3**表中，**"课程"** & **"成绩"** 则在**score_lx3**表中
  - 需要 **多表联结** 进行查询，联结条件为 **"学号"**
  - 多表联结：
    - 内联结：根据条件取两表的公共数据
    - 左外联结：取 join 左表的所有数据，根据条件关联查询 join 右表的数据，不符合条件以null值填充
    - 右外联结：取 join 右表的所有数据，根据条件关联查询 join 左表的数据，不符合条件以null值填充



```mysql
select a.id,a.name,b.course,b.score 
from student_lx3 as a left join score_lx3 as b 
on a.id = b.id;

+----+--------+--------+-------+
| id | name   | course | score |
+----+--------+--------+-------+
|  1 | 张三   | 语文   |    90 |
|  1 | 张三   | 数学   |    67 |
|  2 | 赵四   | 语文   |    88 |
|  3 | 李五   | 数学   |    99 |
|  3 | 李五   | 英语   |    77 |
|  4 | 王六   | NULL   |  NULL |
+----+--------+--------+-------+
```
</br>

### **多表联接 - 外联接**

</br>

**[题目]**

现在有两个表：

- **学生表**：记录了学生的基本信息，字段有 **"学号"**、**"姓名"**
- **近视学生表**：记录了序号以及学生学号
- 两张表通过**学号**进行关联

现在需要找出不是近视的学生都有哪些？

```mysql
#建学生表

create table student_lx4(
    id int not null comment "学号",
    name varchar(64) not null comment "姓名");
    
# 查看
desc student_lx4;

+-------+-------------+------+-----+---------+-------+
| Field | Type        | Null | Key | Default | Extra |
+-------+-------------+------+-----+---------+-------+
| id    | int(11)     | NO   |     | NULL    |       |
| name  | varchar(64) | NO   |     | NULL    |       |
+-------+-------------+------+-----+---------+-------+

# 插入数据
insert into student_lx4(id,name) 
values 
(0001,"周周"),(0002,"李李"),(0003,"王网"),(0004,"张张"),(0005,"猴后");

#查看数据
select * from student_lx4;

+----+---------+
| id | name    |
+----+---------+
|  1 | 周周    |
|  2 | 李李    |
|  3 | 王网    |
|  4 | 张张    |
|  5 | 猴后    |
+----+---------+

#建近视学生表
create table jinshi(
    id int not null comment "序号",
    s_id int not null comment "学生学号");
    
#查看数据表

desc jinshi;

+-------+---------+------+-----+---------+-------+
| Field | Type    | Null | Key | Default | Extra |
+-------+---------+------+-----+---------+-------+
| id    | int(11) | NO   |     | NULL    |       |
| s_id  | int(11) | NO   |     | NULL    |       |
+-------+---------+------+-----+---------+-------+

#插入数据
insert into jinshi(id,s_id) 
values 
(1,0001),(2,0002),(3,0004);

#查看数据
select * from jinshi;
+----+------+
| id | s_id |
+----+------+
|  1 |    1 |
|  2 |    2 |
|  3 |    4 |
+----+------+
```
</br>

**[思路]**

- 读题：**不是**近视的学生都有**谁**？
- 解题：
  - 学生表信息包含近视学生表的信息，现在要找出不在近视学生表中但在学生表中的数据，使用left join，学生表为左表
  - 根据笛卡尔乘积，可将筛选条件设定为left join 后近视学生表序号为空的数据行



```mysql
select st.id,st.name
from student_lx4 as st left join jinshi as js 
on st.id = js.s_id
where js.id is null;

+----+--------+
| id | name   |
+----+--------+
|  3 | 王网   |
|  5 | 猴后   |
+----+--------+
```
</br>

### **行列转换**
</br>

**[题目]**

cook表记录了三个字段，分别是年、月、值

现需要进行行列转换为：年、m1、m2、m3、m4

```mysql
#创建表
create table cook(
    year int comment "年",
    month int comment "月",
    price float comment "值"
);

#查看表
desc cook;
+-------+---------+------+-----+---------+-------+
| Field | Type    | Null | Key | Default | Extra |
+-------+---------+------+-----+---------+-------+
| year  | int(11) | YES  |     | NULL    |       |
| month | int(11) | YES  |     | NULL    |       |
| price | float   | YES  |     | NULL    |       |
+-------+---------+------+-----+---------+-------+

#插入数据
insert into cook(year,month,price) values
(2009,1,1.1),
(2009,2,1.2),
(2009,3,1.3),
(2009,4,1.4),
(2010,1,2.1),
(2010,2,2.2),
(2010,3,2.3),
(2010,4,2.4);

#查看数据
select * from cook;
+------+-------+-------+
| year | month | price |
+------+-------+-------+
| 2009 |     1 |   1.1 |
| 2009 |     2 |   1.2 |
| 2009 |     3 |   1.3 |
| 2009 |     4 |   1.4 |
| 2010 |     1 |   2.1 |
| 2010 |     2 |   2.2 |
| 2010 |     3 |   2.3 |
| 2010 |     4 |   2.4 |
+------+-------+-------+
```

</br>

**[思路]**

- 读题：行列转换为：年、m1、m2、m3、m4
- 解题：
  - 按照年进行分组，得到年这一列
  - 用case when将原先月这列进行赋值转换，值为原先的"值"一列，并生成新的m1、m2、m3、m4列
  - 去除为0的项，可以直接使用max函数，前提是原先"值"该列类型为数值

```mysql
select year,
max(case when month = "1" then price else 0 end) as "m1",
max(case when month = "2" then price else 0 end) as "m2",
max(case when month = "3" then price else 0 end) as "m3",
max(case when month = "4" then price else 0 end) as "m4"
from cook
group by year;

+------+------------+------------+------------+------------+
| year | m1         | m2         | m3         | m4         |
+------+------------+------------+------------+------------+
| 2009 |  1.1       | 1.2        | 1.3        |  1.4       |
| 2010 |  2.1       | 2.2        | 2.3        |  2.4       |
+------+------------+------------+------------+------------+

```
</br>

### MySQL5.7中的排名
</br>

> MySQL 5.7实现排名(MySQL8.0 中的rank()、row_number())

</br>

[题目]

用户访问表中记录了用户的访问信息，字段有：用户编号、用户类型、访问次数



```mysql
#创建表
create table visits(
    uid int not null comment "用户编号",
    utype varchar(10) comment "用户类型",
    vtimes int comment "访问次数"
);

#插入数据
insert into visits(uid,utype,vtimes) 
values 
(10,"A",352),(6,"C",209),(7,"C",110),
(4,"E",101),(2,"B",53),(20,"A",53),
(11,"C",32),(1,"A",30),(9,"E",29),(8,"B",6);


#有相同数不并列排名 - 8.0中的row_number()
select v.uid,v.utype,v.vtimes,@rank := @rank + 1 as "rank"
from visits as v,(select @rank :=0 as "rank") t
order by v.vtimes desc;

+-----+-------+--------+------+
| uid | utype | vtimes | rank |
+-----+-------+--------+------+
|  10 | A     |    352 |    1 |
|   6 | C     |    209 |    2 |
|   7 | C     |    110 |    3 |
|   4 | E     |    101 |    4 |
|   2 | B     |     53 |    5 |
|  20 | A     |     53 |    6 |
|  11 | C     |     32 |    7 |
|   1 | A     |     30 |    8 |
|   9 | E     |     29 |    9 |
|   8 | B     |      6 |   10 |
+-----+-------+--------+------+


#有相同数时同排名，并跳跃排名 - 8.0中的rank()
select v.uid,v.utype,v.vtimes,@rownum := @rownum + 1 as "rownum",
(
    case when @prevtimes = v.vtimes then @rank
    when @prevtimes := v.vtimes then @rank := @rownum
    end
) as "rank"
from visits as v,(select 
                  	@rownum := 0 as "rownum",
                  	@rank := 0 as "rank",
                  	@prevtimes := NULL as "prevtimes" 
                 ) t
order by v.vtimes desc;

+-----+-------+--------+--------+------+
| uid | utype | vtimes | rownum | rank |
+-----+-------+--------+--------+------+
|  10 | A     |    352 |      1 | 1    |
|   6 | C     |    209 |      2 | 2    |
|   7 | C     |    110 |      3 | 3    |
|   4 | E     |    101 |      4 | 4    |
|   2 | B     |     53 |      5 | 5    |
|  20 | A     |     53 |      6 | 5    |
|  11 | C     |     32 |      7 | 7    |
|   1 | A     |     30 |      8 | 8    |
|   9 | E     |     29 |      9 | 9    |
|   8 | B     |      6 |     10 | 10   |
+-----+-------+--------+--------+------+


#有相同数时，连续排名 - 8.0中的dense_rank()

select v.*,
(case 
 	when @prevtimes = v.vtimes then @rank
	when @prevtimes := v.vtimes then @rank := @rank +1
 	end
) as "rank"
from visits as v,(select @rank := 0 as "rank",@prevtimes := NULL as "prevtimes") t
order by v.vtimes desc;

+-----+-------+--------+------+
| uid | utype | vtimes | rank |
+-----+-------+--------+------+
|  10 | A     |    352 | 1    |
|   6 | C     |    209 | 2    |
|   7 | C     |    110 | 3    |
|   4 | E     |    101 | 4    |
|   2 | B     |     53 | 5    |
|  20 | A     |     53 | 5    |
|  11 | C     |     32 | 6    |
|   1 | A     |     30 | 7    |
|   9 | E     |     29 | 8    |
|   8 | B     |      6 | 9    |
+-----+-------+--------+------+
```

</br>

持续更新中～
