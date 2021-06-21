---
title: 每日一题 - 寻找第N高的数据
date: 2021-05-29 20:41:02
tags:
    - MySQL练习题
categories:
    - [SQL,MySQL]
---

> 摘要
MySQL练习题，如何查找第N高的数据
<!-- more -->

### MySQL练习题

<br>

#### [题目]

---

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


<br>

#### [解题思路]

---


- 读题：找出**语文课**中成绩**第二高**的学生成绩，如果**不存在第二高成绩**的学生，那么查询应**返回 null** 。
- 解题
  - 找出所有选修了"语文"课的学生成绩
  - 进行排序并选择第二高成绩
  - 特殊情况：如果不存在第二高的成绩，返回null

```mysql
# 
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


<br>
<br>

---
**特别声明**：以上内容均来自网络，侵删，望留言告知，谢谢～


参考文章:  
