---
title: MySQL练习 - 场景模拟初级篇(持续更新)
thumbnail: 
date: 2021-10-6 22:33:36
toc: true
tags:
    - MySQL练习题
categories:
    - [SQL,MySQL]
---

> 摘要
MySQL练习题，基于不同的场景模拟进一步加强MySQl知识点练习～
<!-- more -->

## MySQL练习 - 场景模拟初级篇

</br>

### 薪水涨幅升序

</br>

**[题目]**

现在有两个表：

- **雇员表**：记录了雇员基本信息，字段包括：雇员编号、出生日期、姓名、性别、雇用日期
- **薪水表**：记录了雇员的**薪水金额**以及**签订周期**，字段包括：雇员编号、薪水、起始日期、结束日期
- 两张表通过雇员编号进行连结

现在需要查找当前所有雇员入职以来的薪水涨幅，给出雇员编号以及其对应的薪水涨幅，并按照薪水涨幅进行升序

注意：薪水表中最大结束日期为2004-01-01，说明结束日期在此之前的均表示为已离职员工



```SQL
#建雇员表

create table employees( 
    id int not null comment "雇员编号", 
    birthday date comment "出生日 期", 
    name varchar(64) comment "姓名", 
    sex varchar(64) comment "性别", 
    employ_date date comment " 雇用日期");

#查看表
desc employees;
+-------------+-------------+------+-----+---------+-------+
| Field       | Type        | Null | Key | Default | Extra |
+-------------+-------------+------+-----+---------+-------+
| id          | int(11)     | NO   |     | NULL    |       |
| birthday    | date        | YES  |     | NULL    |       |
| name        | varchar(64) | YES  |     | NULL    |       |
| sex         | varchar(64) | YES  |     | NULL    |       |
| employ_date | date        | YES  |     | NULL    |       |
+-------------+-------------+------+-----+---------+-------+

#插入数据
insert into employees(id,birthday,name,sex,employ_date) 
values 
(10002,"1976-09-09","周周","女","2001-08-02"),
(10005,"1978-09-09","小明","男","2001-09-09"),
(10006,"1979-08-29","西西","女","2001-08-02")
;

#查看数据
select * from employees;
+-------+------------+--------+------+-------------+
| id    | birthday   | name   | sex  | employ_date |
+-------+------------+--------+------+-------------+
| 10002 | 1976-09-09 | 周周   | 女   | 2001-08-02  |
| 10005 | 1978-09-09 | 小明   | 男   | 2001-09-09  |
| 10006 | 1979-08-29 | 西西   | 女   | 2001-08-02  |
+-------+------------+--------+------+-------------+


#建薪水表
create table salary(
    id int not null comment "雇员编号",
    pay int comment "薪资",
    start_date date comment "起始日期",
    finish_date date comment "结束日期");

#查看表
desc salary;
+-------------+---------+------+-----+---------+-------+
| Field       | Type    | Null | Key | Default | Extra |
+-------------+---------+------+-----+---------+-------+
| id          | int(11) | NO   |     | NULL    |       |
| pay         | int(11) | YES  |     | NULL    |       |
| start_date  | date    | YES  |     | NULL    |       |
| finish_date | date    | YES  |     | NULL    |       |
+-------------+---------+------+-----+---------+-------+

#插入数据
insert into salary(id,pay,start_date,finish_date) 
values 
(10002,72527,"2001-08-02","2003-01-01"),
(10002,75432,"2003-01-01","2004-01-01"),
(10005,94692,"2001-09-09","2003-01-01"),
(10006,43311,"2001-08-02","2004-01-01")
;

#查看数据
select * from salary;
+-------+-------+------------+-------------+
| id    | pay   | start_date | finish_date |
+-------+-------+------------+-------------+
| 10002 | 72527 | 2001-08-02 | 2003-01-01  |
| 10002 | 75432 | 2003-01-01 | 2004-01-01  |
| 10005 | 94692 | 2001-09-09 | 2003-01-01  |
| 10006 | 43311 | 2001-08-02 | 2004-01-01  |
+-------+-------+------------+-------------+

```

</br>

**[思路]**

- **读题**：查找**当前所有雇员**入职以来的**薪水涨幅**，给出雇员编号以及对应的薪水涨幅，并按照薪水涨幅进行升序
- **解题**：
  - 输出为：雇员编号、薪水涨幅，且按照薪水涨幅进行升序排列
  - 限定条件为：当前雇员，即结束日期为表中最大日期2004 - 01 - 01
  - 薪水涨幅 = 当前薪水 - 入职薪水 ，根据最终输出要求，先分别找出雇员编号+当前薪水作为临时表a，在找出雇员编号+入职薪水作为临时表b，最后将临时表a和临时表b进行join方能运算出结果



```SQL

select a.id,(a.pay - b.pay) as "薪水涨幅" 
from 
(select id,pay from salary where finish_date = "2004-01-01") as a 
join 
(select e.id,s.pay from 
 employees as e 
 join salary as s 
 on e.id = s.id 
 where e.employ_date = s.start_date 
 and 
 e.id in 
 (select id from salary where finish_date = "2004-01-01")) as b 
 on a.id = b.id 
 order by "薪水涨幅";
 
 
+-------+--------------+
| id    | 薪水涨幅     |
+-------+--------------+
| 10002 |         2905 |
| 10006 |            0 |
+-------+--------------+

```

</br>

### 比前一天高的数据
</br>

**[题目]**

"日销"表记录了某公司每天的营业额，字段有：id，日期，营业额(万元)

现在需要找出所有比前一天(昨天)营业额更高的数据

```SQL
#创建表

create table daysale(
    id int not null,
    saledate date comment "日期",
    turnover int comment "营业额(万元)");
    
#查看表
desc daysale;
+----------+---------+------+-----+---------+-------+
| Field    | Type    | Null | Key | Default | Extra |
+----------+---------+------+-----+---------+-------+
| id       | int(11) | NO   |     | NULL    |       |
| saledate | date    | YES  |     | NULL    |       |
| turnover | int(11) | YES  |     | NULL    |       |
+----------+---------+------+-----+---------+-------+

#插入数据
insert into daysale(id,saledate,turnover) 
values 
(1,"2019-01-01",97),
(2,"2019-01-02",87),
(3,"2019-01-03",88),
(4,"2019-01-04",98),
(5,"2019-01-05",100),
(6,"2019-01-06",80),
(7,"2019-01-07",77),
(8,"2019-01-08",92);

#查看数据
select * from daysale;
+----+------------+----------+
| id | saledate   | turnover |
+----+------------+----------+
|  1 | 2019-01-01 |       97 |
|  2 | 2019-01-02 |       87 |
|  3 | 2019-01-03 |       88 |
|  4 | 2019-01-04 |       98 |
|  5 | 2019-01-05 |      100 |
|  6 | 2019-01-06 |       80 |
|  7 | 2019-01-07 |       77 |
|  8 | 2019-01-08 |       92 |
+----+------------+----------+
```

</br>

**[思路]**

- 读题：找出所有**营业额**比**前一天(昨天)**营业额更高的数据
- 解题：
  - 比较同一个表内不同行的内容需要自join，连结条件为日期差为1天
    - 日期比较方式1：datediff(日期1,日期2) ，即日期1 - 日期2的天数差
    - 日期比较方式2：timestampdiff(day/hour/second,日期1,日期2)，即日期2 - 日期1的差，与上面相反
  - 结果限定条件为：营业额比前一天的高



```SQL
#datediff

select b.* 
from daysale as a join daysale as b 
on datediff(b.saledate,a.saledate) = 1
where b.turnover > a.turnover;

+----+------------+----------+
| id | saledate   | turnover |
+----+------------+----------+
|  3 | 2019-01-03 |       88 |
|  4 | 2019-01-04 |       98 |
|  5 | 2019-01-05 |      100 |
|  8 | 2019-01-08 |       92 |
+----+------------+----------+

#timestempdiff
select b.* 
from daysale as a join daysale as b 
on timestampdiff(day,a.saledate,b.saledate) = 1
where b.turnover > a.turnover;

+----+------------+----------+
| id | saledate   | turnover |
+----+------------+----------+
|  3 | 2019-01-03 |       88 |
|  4 | 2019-01-04 |       98 |
|  5 | 2019-01-05 |      100 |
|  8 | 2019-01-08 |       92 |
+----+------------+----------+

```

</br>

### 最小的n个数
</br>
[题目]

现在有两个表：

- 学生表里记录了学生的学号、入学时间等信息，字段有：姓名、学号、班级、入学时间、年龄、专业
- 成绩表里记录了学生选课成绩的信息，字段有：学号、课程号、分数
- 两张表通过学号进行连结

现在需要：

- 筛选出2017年入学的"计算机"专业年龄最小的3位同学，输出格式为姓名、年龄
- 统计每个班各位同学成绩平均分大于80分的人数和人数占比



```SQL
#创建学生表

create table stu_07(
    name varchar(64) comment "姓名",
    id int not null comment "学号",
    class varchar(64) comment "班级",
    entrancedate date comment "入学时间",
    age int comment "年龄",
    professional varchar(64) comment "专业");


#查看表
desc stu_07;
+--------------+-------------+------+-----+---------+-------+
| Field        | Type        | Null | Key | Default | Extra |
+--------------+-------------+------+-----+---------+-------+
| name         | varchar(64) | YES  |     | NULL    |       |
| id           | int(11)     | NO   |     | NULL    |       |
| class        | varchar(64) | YES  |     | NULL    |       |
| entrancedate | date        | YES  |     | NULL    |       |
| age          | int(11)     | YES  |     | NULL    |       |
| professional | varchar(64) | YES  |     | NULL    |       |
+--------------+-------------+------+-----+---------+-------+

#插入数据
insert into stu_07(name,id,class,entrancedate,age,professional) 
values 
("赵一",0001,"1班","2016-09-01",19,"计算机"),
("钱二",0002,"1班","2017-09-01",21,"计算机"),
("孙三",0003,"2班","2017-09-01",19,"金融"),
("李四",0004,"3班","2017-09-01",17,"计算机"),
("周周",0005,"3班","2017-09-01",20,"计算机"),
("吴五",0006,"3班","2017-09-01",18,"计算机");

#查看数据
select * from stu_07;
+--------+----+-------+--------------+------+--------------+
| name   | id | class | entrancedate | age  | professional |
+--------+----+-------+--------------+------+--------------+
| 赵一   |  1 | 1班   | 2016-09-01   |   19 | 计算机       |
| 钱二   |  2 | 1班   | 2017-09-01   |   21 | 计算机       |
| 孙三   |  3 | 2班   | 2017-09-01   |   19 | 金融         |
| 李四   |  4 | 3班   | 2017-09-01   |   17 | 计算机       |
| 周周   |  5 | 3班   | 2017-09-01   |   20 | 计算机       |
| 吴五   |  6 | 3班   | 2017-09-01   |   18 | 计算机       |
+--------+----+-------+--------------+------+--------------+

#创建成绩表

create table score_07(id int not null comment "学号",courseid int comment "课程号",score int comment "分数");

#查看表
desc score_07;
+----------+---------+------+-----+---------+-------+
| Field    | Type    | Null | Key | Default | Extra |
+----------+---------+------+-----+---------+-------+
| id       | int(11) | NO   |     | NULL    |       |
| courseid | int(11) | YES  |     | NULL    |       |
| score    | int(11) | YES  |     | NULL    |       |
+----------+---------+------+-----+---------+-------+

#插入数据
insert into score_07(id,courseid,score) 
values
(0001,01,90),
(0002,01,70),
(0002,02,84),
(0003,01,90),
(0003,03,80),
(0004,01,90),
(0004,02,60),
(0005,01,85),
(0006,02,70);

#查看数据
select * from score_07;
+----+----------+-------+
| id | courseid | score |
+----+----------+-------+
|  1 |        1 |    90 |
|  2 |        1 |    70 |
|  2 |        2 |    84 |
|  3 |        1 |    90 |
|  3 |        3 |    80 |
|  4 |        1 |    90 |
|  4 |        2 |    60 |
|  5 |        1 |    85 |
|  6 |        2 |    70 |
+----+----------+-------+
```
</br>

**[解题1]**

- 读题：筛选出**2017年入学**的"**计算机**"专业**年龄最小**的**3位**同学，输出格式为**姓名、年龄**
- 解题：
  - 限定条件1：2017年入学，根据入学时间的年份进行限定
  - 限定条件2：专业 - 计算机
  - 限定条件3：年龄最小的3位同学，根据年龄进行倒序排列，limit输出前3行

```SQL
select name,age
from stu_07
where year(entrancedate) = 2017
and professional = "计算机"
order by age
limit 3;

+--------+------+
| name   | age  |
+--------+------+
| 李四   |   17 |
| 吴五   |   18 |
| 周周   |   20 |
+--------+------+
```
</br>

**[解题2]**

- 读题：统计**每个班**各位同学**成绩平均分**大于**80**分的**人数**和**人数占比**
- 解题：
  - 每位同学成绩平均分 > 80，成绩表根据 id 进行分组算出平均分，作为临时表
  - 统计每个班，则需要按照班级来分组，班级信息在学生表，而平均分信息在临时表，需要两表进行join
  - 输出为人数、人数占比，使用case when 进行统计平均分大于80的人数，以及后续的计算占比

```SQL
select 
sum(case when 平均成绩 > 80 then 1 else 0 end) as "人数",
sum(case when 平均成绩 > 80 then 1 else 0 end)/count(st.id) as "人数占比"
from stu_07 as st 
left join
(select id,avg(score) as 平均成绩 from score_07 group by id) 
as a 
on st.id = a.id
group by st.class;

+--------+--------------+
| 人数   | 人数占比     |
+--------+--------------+
|      1 |       0.5000 |
|      1 |       1.0000 |
|      1 |       0.3333 |
+--------+--------------+
```

</br>

### 连续出现n次的数据
</br>

**[题目]**

成绩表记录了学生id以及分数，字段有：id、score

先需要查找出所有至少连续出现3次的分数

```SQL
#创建表
create table score_09(
    id int not null comment "学号",
    score int comment "分数"
);

#查看表
desc score_09;                                                                           +-------+---------+------+-----+---------+-------+
| Field | Type    | Null | Key | Default | Extra |
+-------+---------+------+-----+---------+-------+
| id    | int(11) | NO   |     | NULL    |       |
| score | int(11) | YES  |     | NULL    |       |
+-------+---------+------+-----+---------+-------+

#插入数据
insert into score_09(id,score) values
(0001,89),(0002,76),(0003,76),
(0004,83),(0005,83),(0006,83),
(0007,77),(0008,90),(0009,88),
(0010,86);

#查看数据
select * from score_09;
+----+-------+
| id | score |
+----+-------+
|  1 |    89 |
|  2 |    76 |
|  3 |    76 |
|  4 |    83 |
|  5 |    83 |
|  6 |    83 |
|  7 |    77 |
|  8 |    90 |
|  9 |    88 |
| 10 |    86 |
+----+-------+
```

</br>

**[思路]**：

- 读题：查找出**所有**、**至少**、**连续出现3次**的分数
- 解题：
  - **注意：这个题目要求学号必须连续，以下语句才能正确执行**
  - 比较同一个表里的同一列的不同行的数据，需要自join
  - 连续出现3次，意思就是学号a的成绩=学号a+1的成绩=学号a+2的成绩，说明需要自join3次
  - 输出为这个至少出现3次的成绩是多少，distinct去除重复

```SQL
select distinct a.score
from score_09 as a join score_09 as b join score_09 as c
on a.id = b.id - 1 and b.id = c.id - 1
where a.score = b.score and b.score = c.score;

+-------+
| score |
+-------+
|    83 |
+-------+
```
</br>

### 课程满意度分析
</br>

**[题目]**

- 满意度记录了教师和学生对课程的满意程度，字段有：教师编号、学生编号、是否满意；
  - 其中是否满意代表老师和学生对课程的评价，值"是"代表教师和学生都满意
- 用户表记录了学校教师和学生的信息，字段有：用户编号、是否在系统、角色
  - 每个用户有唯一的编号
  - 是否在系统表示 这个用户是否还在这所学校里
  - 角色表示这个用户是教师还是学生
- 满意度表中的学生编号、教师编号与用户表的编号联结

现需要分析学校里人员对课程的满意度

</br>

```SQL
#创建满意度表
create table satisfaction(
    tno varchar(10) comment "教师编号",
    sno varchar(10) comment "学生编号",
    satisfied varchar(64) comment "是否满意"
);

#查看表
desc satisfaction;
+-----------+-------------+------+-----+---------+-------+
| Field     | Type        | Null | Key | Default | Extra |
+-----------+-------------+------+-----+---------+-------+
| tno       | varchar(10) | YES  |     | NULL    |       |
| sno       | varchar(10) | YES  |     | NULL    |       |
| satisfied | varchar(64) | YES  |     | NULL    |       |
+-----------+-------------+------+-----+---------+-------+

#插入数据
insert into satisfaction(tno,sno,satisfied)
values
("01","2","学生不满意"),
("01","1","是"),
("02","1","老师不满意"),
("02","2","是"),
("03","1","是"),
("03","2","是");

#查看数据
select * from satisfaction;
+------+------+-----------------+
| tno  | sno  | satisfied       |
+------+------+-----------------+
| 01   | 2    | 学生不满意       |
| 01   | 1    | 是              |
| 02   | 1    | 老师不满意       |
| 02   | 2    | 是              |
| 03   | 1    | 是              |
| 03   | 2    | 是              |
+------+------+-----------------+

#创建用户表
create table sch_user(
    id varchar(10) not null primary key comment "编号",
    in_not_sys varchar(64) comment "是否在系统中",
    roles varchar(64) comment "角色"
);

#查看表
desc sch_user;
+------------+-------------+------+-----+---------+-------+
| Field      | Type        | Null | Key | Default | Extra |
+------------+-------------+------+-----+---------+-------+
| id         | varchar(10) | NO   | PRI | NULL    |       |
| in_not_sys | varchar(64) | YES  |     | NULL    |       |
| roles      | varchar(64) | YES  |     | NULL    |       |
+------------+-------------+------+-----+---------+-------+

#插入数据
insert into sch_user(id,in_not_sys,roles)
values
("1","是","学生"),
("2","是","学生"),
("01","是","教师"),
("02","否","教师"),
("03","是","教师");

#查看数据
select * from sch_user;
+----+------------+--------+
| id | in_not_sys | roles  |
+----+------------+--------+
| 01 | 是         | 教师   |
| 02 | 否         | 教师   |
| 03 | 是         | 教师   |
| 1  | 是         | 学生   |
| 2  | 是         | 学生   |
+----+------------+--------+
```

</br>

[思路]

- 读题：现需要分析**学校里**人员对课程的**满意度**
- 解题：
  - 满意度信息在满意度表中，人员信息在用户表中，需要两表联结
  - 满意度计算：(对课程都满意且在系统中的教师和学生) / (在系统中的所有用户)
  - 限定条件1：是否在系统 - 是
  - 限定条件2：满意度 - 是
  - 找出在系统中的所有id，然后筛选满意度表中的数据，再进行计算

```SQL
select sum(if(a.satisfied = "是",1,0)) / count(a.satisfied) as "课程满意度"
from satisfaction as a
where a.tno in (select id from sch_user where in_not_sys = "是")
and a.sno in (select id from sch_user where in_not_sys = "是");

+-----------------+
| 课程满意度      |
+-----------------+
|          0.7500 |
+-----------------+
```

</br>

### 红包领取情况
</br>

**[题目]**

- 用户活跃表记录了用户的登录信息，字段有：登录日期、用户ID、新用户
  - 其中新用户列的值为0、1；值为0为老用户、值为1为新用户
- 领取红包表里记录了用户领取红包的信息，字段有：抢红包日期、抢红包时间、用户id、金额



现需要分析以下问题：

- 计算2019年6月1日至今，每天DAU (即活跃用户，定义：有登录的用户)
- 分析每天领红包的用户数、人均领取金额、人均领取次数，其中还有用户属性以及领取红包未登录的情况
- 分析每个月按领红包取天数为1、2、3......30、31天区分，计算取每个月领取红包的用户数、人均领取金额、人均领取次数
- 分析每个月领过红包用户和未领红包用户的数量

</br>

```SQL
create table dau_users(
    log_date varchar(64) comment "登录日期",
    uid int comment "用户id",
    new_old tinyint comment "新用户"
);

create table grabred(
    grab_date varchar(64) comment "抢红包日期",
    grab_time datetime comment "抢红包时间",
    uid int comment "用户id",
    amount float comment "金额"
);

```

</br>

**[解题1]**

- 读题：计算**2019年6月1日至今**，**每天**DAU (即活跃用户，定义：**有登录的用户**)
- 解题
  - 2019年6月1日到今天，限定条件为登录日期 >= 2019年6月1日
  - 统计每天的登录用户

```SQL
select log_date,count(uid) as "DAU-活跃用户"
from dau_users
where log_date >= "20190601"
group by log_date;
```

</br>

**[解题2]**

- 读题：分析**每天**领红包的**用户数**、**人均**领取**金额**、**人均**领取**次数**，其中还有用户属性以及领取红包未登录的情况
- 解题
  - 每天领红包的用户分为三种：新用户、老用户以及领取红包但是未登录的用户，而新用户、老用户的数据统计在用户活跃表中，领取红包但是未登录的用户需要用两表进行联结比对
  - 人均领取金额 = 领红包总金额 / 领红包总人数 (去重用户数)
  - 人均领取次数 = 总领取次数 / 领红包总人数 (去重用户数)



```SQL
select c.grabdate,
count(distinct case when c.new_or_old = "新用户" then uid else null end) as "日领新用户数",
count(distinct case when c.new_or_old = "老用户" then uid else null end) as "日领老用户数",
count(distinct case when c.new_or_old = "未登录用户" then uid else null end) as "日领未登录用户数",
sum(c.amount)/ count(distinct c.uid) as "人均领取金额",
count(*)/ count(distinct c.uid) as "人均领取次数"
from
(select b.grabdate,b.uid,b.amount,
(
    case when a.new_old = 1 then "新用户",
 		 when a.new_old = 0 then "老用户",
    	 else "未登录用户"
end) as new_or_old
from dau_users as a right join grabred as b
on a.log_date = b.grabdate and a.uid = b.uid) as c
group by c.grabdate;
```

</br>

**[解题3]**

- 读题：分析**每个月**按领红包取天数为1、2、3......30、31天区分，计算取每个月领取红包的**用户数**、**人均**领取**金额**、**人均**领取**次数**
- 解题：
  - 按照月份进行分组，然后统计该月有多少天有领红包行为，因为领红包日期为字符串格式，所以选用领红包时间列使用month()函数进行分组
  - 统计分组后每个月有多少用户领取红包，需要distinct
  - 人均领取金额 = 该月份领取红包总金额 / 该月份领取红包用户数 (distinct)
  - 人均领取次数 = 该月份总领取红包次数 / 该月份领取红包用户数 (distinct)



```SQL
select month(g.grab_time) as "月份",
count(distinct g.grab_time) as "领取天数"
count(distinct uid) as "用户数",
sum(g.amount) / count(distinct uid) as "人均领取金额",
count(*) / count(distinct uid) as "人均领取次数"
from grabred as g
group by month(g.grab_time);


#小问题1：这样显示月份并不准确，month()筛选分组后显示的只是月份，即03、04,如果有年份的区别，例如2019、2018这样的就会使数据不明确
#解决：1、再取出年份进行拼接concat() ；2、直接用领红包日期来分组，因为是字符串，所以使用转换格式，然后在month()，或者截取前6个数字进行分组，这需要数据录入的格式以及准确性保持很高的要求

```

</br>

[解题4]

- 读题：分析**每个月** **领过**红包用户和**未领**红包用户的数量
- 解题：
  - 每个月，即按照月份来分组，month()
  - 领过红包用户信息存在领红包表中，而未领红包用户存在用户活跃表中，需要两表联结进行比对，用户活跃表作为 left join 的左表
  - 未领取红包用户 = 活跃用户 - 领红包用户

</br>

```SQL
select month(c.grab_time),
sum(case when 是否领过红包 = "领过红包用户" then 1 else 0 end) as "领过红包用户数",
sum(case when 是否领过红包 = "未领过红包用户" then 1 else 0 end) as "未领过红包用户数"
from
(select b.grab_time,a.uid,b.uid as "领红包用户id",
 (
     case when b.uid is not null then "领过红包用户"
     else "未领过红包用户"
 end) as "是否领过红包"
from dau_users as a 
left join grabred as b 
on a.log_date = b.grab_date and a.uid = b.uid) as c
group by month(c.grab_time);

#注意点
#1、还是跟问题3一样的月份函数的使用列问题；2、要注意联结后的列名在之后的语句中使用，即使用时要准确无误的引用
```

</br>

### 登录统计排序
</br>

**[题目]**

用户登录时间表中记录了用户登录的信息，字段有：用户id、姓名、邮箱、最后登录时间

现需要输出一张表，字段为：姓名、最后登录时间、登录时间排名、登录天数排名

- 登录时间排名：按时间给出每个人的登录次数，登录时间最早为1，依次排下去
- 登录天数排名：按天给出每个人的登录次数，同一天多次登录设定为同一次，最多天数标记为1，之后依次类推

</br>

```SQL
#创建表
create table user_loginfo(
    uid int not null comment "用户id",
    name varchar(64) comment "姓名",
    email_addr varchar(64) comment "邮箱地址",
    last_logtime datetime comment "最后登录时间"
);

#查看数据表
desc user_loginfo;
+--------------+-------------+------+-----+---------+-------+
| Field        | Type        | Null | Key | Default | Extra |
+--------------+-------------+------+-----+---------+-------+
| uid          | int(11)     | NO   |     | NULL    |       |
| name         | varchar(64) | YES  |     | NULL    |       |
| email_addr   | varchar(64) | YES  |     | NULL    |       |
| last_logtime | datetime    | YES  |     | NULL    |       |
+--------------+-------------+------+-----+---------+-------+

#插入数据
insert into user_loginfo(uid,name,email_addr,last_logtime)
values 
(100,"test4","test4@163.com","2007/11/25 16:31"),
(13,"test1","test1@163.com","2007/3/22 16:27"),
(19,"test1","test1@163.com","2007/10/25 14:13"),
(42,"test1","test1@163.com","2007/10/25 14:20"),
(45,"test2","test2@163.com","2007/4/25 14:17"),
(49,"test2","test2@163.com","2007/5/25 14:22");

#查看数据
select * from user_loginfo;
+-----+-------+---------------+---------------------+
| uid | name  | email_addr    | last_logtime        |
+-----+-------+---------------+---------------------+
| 100 | test4 | test4@163.com | 2007-11-25 16:31:00 |
|  13 | test1 | test1@163.com | 2007-03-22 16:27:00 |
|  19 | test1 | test1@163.com | 2007-10-25 14:13:00 |
|  42 | test1 | test1@163.com | 2007-10-25 14:20:00 |
|  45 | test2 | test2@163.com | 2007-04-25 14:17:00 |
|  49 | test2 | test2@163.com | 2007-05-25 14:22:00 |
+-----+-------+---------------+---------------------+
```

</br>

**[思路]**

- 读题：现需要输出一张表，字段为：**姓名**、**最后登录时间**、**登录时间排名**、**登录天数排名**

  - 登录时间排名：**按时间**给出每个人的**登录次数**，登录时间**最早为1**，依次排下去
  - 登录天数排名：**按天**给出每个人的登录次数，同一天**多次登录设定为同一次**，最**多天数标记为1**，之后依次类推

- 解题(与出题者给出的答案理解不同，出于我自设定的业务场景)：

  - 我觉得应该需要给出以下这样的列表

    | 姓名  | 最后登录时间        | 最早登录时间        | 登录时间排名 | 登录天数 | 登录天数排名 |
    | ----- | ------------------- | ------------------- | ------------ | -------- | ------------ |
    | test1 | 2007-05-25 14:22:00 | 2007-02-25 14:17:00 | 1            | 2        | 1            |
    | test2 | 2007-10-25 14:20:00 | 2007-03-22 16:27:00 | 2            | 2        | 1            |
    | test4 | 2007-11-25 16:31:00 | 2007-11-25 16:31:00 | 3            | 1        | 2            |

  - 下面语句分开的原因是：这种方式语句需要对要进行排名的列进行先排序，而同时对两列进行排列，会影响第二列的排名不准确，暂时不知道如何处理

</br>

```SQL
select a.name,a.最后登录时间,a.最早登录时间,
(case
 	when @predate = a.最早登录时间 then @rank
    when @predate := a.最早登录时间 then @rank := @rank + 1
 	end
) as "登录时间排名"
from
(select name,max(last_logtime) as "最后登录时间",min(last_logtime) as "最早登录时间"
from user_loginfo
group by name) as a,
(select @rank := 0 as "rank",@predate := NULL as "predate") as t
order by a.最早登录时间
;

+-------+---------------------+---------------------+--------------------+
| name  | 最后登录时间          | 最早登录时间          | 登录时间排名         |
+-------+---------------------+---------------------+--------------------+
| test2 | 2007-05-25 14:22:00 | 2007-02-25 14:17:00 |                  1 |
| test1 | 2007-10-25 14:20:00 | 2007-03-22 16:27:00 |                  2 |
| test4 | 2007-11-25 16:31:00 | 2007-11-25 16:31:00 |                  3 |
+-------+---------------------+---------------------+--------------------+


select a.name,a.最后登录时间,a.登录天数,
(case
 	when @predate = a.登录天数 then @rank
    when @predate := a.登录天数 then @rank := @rank + 1
 	end
) as "登录天数排名"
from
(select name,max(last_logtime) as "最后登录时间",
 count(distinct date(last_logtime)) as "登录天数"
from user_loginfo
group by name) as a,
(select @rank := 0 as "rank",@pretimes := NULL as "pretimes") as t
order by a.登录天数 desc
;

+-------+---------------------+--------------+--------------------+
| name  | 最后登录时间          | 登录天数      | 登录天数排名         |
+-------+---------------------+--------------+--------------------+
| test1 | 2007-10-25 14:20:00 |            2 |                  1 |
| test2 | 2007-05-25 14:22:00 |            2 |                  1 |
| test4 | 2007-11-25 16:31:00 |            1 |                  2 |
+-------+---------------------+--------------+--------------------+
```

</br>







**持续更新中～**





