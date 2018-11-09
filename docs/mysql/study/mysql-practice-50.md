# 50 道 SQL 练习题

## 数据表介绍

1. 学生表
    Student(s_id, s_name, s_age, s_sex)
    -- s_id 学生编号, s_name 学生姓名, s_age 出生年月, s_sex 学生性别
2. 课程表
    Course(c_id, c_name, t_id)
    --  c_id 课程编号, c_name 课程名称, t_id 教师编号
3. 教师表
    Teacher(t_id, t_name)
    -- t_id 教师编号, t_name 教师姓名
4. 成绩表
    Score(s_id, c_id, s_score)
    -- s_id 学生编号, c_id 课程编号, s_score 分数

## 数据初始化

### 学生表 Student
```sql
create table Student(
    s_id varchar(10) comment '学生编号', 
    s_name varchar(10) comment '学生姓名', 
    s_birth datetime comment '出生年月', 
    s_sex varchar(10) comment '性别'
);
insert into Student values('01' , '赵雷' , '1990-01-01' , '男');
insert into Student values('02' , '钱电' , '1990-12-21' , '男');
insert into Student values('03' , '孙风' , '1990-12-20' , '男');
insert into Student values('04' , '李云' , '1990-12-06' , '男');
insert into Student values('05' , '周梅' , '1991-12-01' , '女');
insert into Student values('06' , '吴兰' , '1992-01-01' , '女');
insert into Student values('07' , '郑竹' , '1989-01-01' , '女');
insert into Student values('09' , '张三' , '2017-12-20' , '女');
insert into Student values('10' , '李四' , '2017-12-25' , '女');
insert into Student values('11' , '李四' , '2012-06-06' , '女');
insert into Student values('12' , '赵六' , '2013-06-13' , '女');
insert into Student values('13' , '孙七' , '2014-06-01' , '女');
```

### 科目表 Course
```sql
create table Course(
    c_id varchar(10) comment '课程编号', 
    c_name nvarchar(10) comment '课程名称', 
    t_id varchar(10) comment '教师编号'
);
insert into Course values('01' , '语文' , '02');
insert into Course values('02' , '数学' , '01');
insert into Course values('03' , '英语' , '03');
```

### 教师表 Teacher
```sql
create table Teacher(
    t_id varchar(10) comment '教师编号', 
    t_name varchar(10) comment '教师姓名'
);
insert into Teacher values('01' , '张三');
insert into Teacher values('02' , '李四');
insert into Teacher values('03' , '王五');
```

### 成绩表 Score
```sql
create table Score(
    s_id varchar(10) comment ' 学生编号', 
    c_id varchar(10) comment '课程编号', 
    s_score decimal(18,1) comment '学生成绩'
);
insert into Score values('01' , '01' , 80);
insert into Score values('01' , '02' , 90);
insert into Score values('01' , '03' , 99);
insert into Score values('02' , '01' , 70);
insert into Score values('02' , '02' , 60);
insert into Score values('02' , '03' , 80);
insert into Score values('03' , '01' , 80);
insert into Score values('03' , '02' , 80);
insert into Score values('03' , '03' , 80);
insert into Score values('04' , '01' , 50);
insert into Score values('04' , '02' , 30);
insert into Score values('04' , '03' , 20);
insert into Score values('05' , '01' , 76);
insert into Score values('05' , '02' , 87);
insert into Score values('06' , '01' , 31);
insert into Score values('06' , '03' , 34);
insert into Score values('07' , '02' , 89);
insert into Score values('07' , '03' , 98);
```

## 数据验证

1. 查询一下课程表，预览创建数据和原始数据是否一样（数据简单，直接查看对比，就能知道是否正确）
```sql
select * FROM Course
```
查看后确认数据一样。  

2. 查询一下成绩表，预览创建数据和原始数据是否一样
```sql
select * FROM Score
```
查看后确认数据一样。  

3. 查询一下学生表，预览创建数据和原始数据是否一样
```sql
select * FROM Student
```
查看后确认数据一样。  

4. 查询一下老师表，预览创建数据和原始数据是否一样
```sql
select * FROM Teacher
```
查看后确认数据一样。   

5. 确定数据没有问题，开始练习。  


## 练习题

1. 查询" 01 "课程比" 02 "课程成绩高的学生的信息及课程分数  
1.1  查询同时存在" 01 "课程和" 02 "课程的情况

1.2  查询存在" 01 "课程但可能不存在" 02 "课程的情况(不存在时显示为 null )

1.3  查询不存在" 01 "课程但存在" 02 "课程的情况

2. 查询平均成绩大于等于 60 分的同学的学生编号和学生姓名和平均成绩

## 查询在 Score 表存在成绩的学生信息
```sql
select 
  st.s_id as s_id, 
  st.s_name as s_name, 
  st.s_birth as s_birth, 
  st.s_sex as s_sex,
  t.s_score
FROM(
  SELECT 
  s_id,  
  sum(s_score) as s_score
  FROM Score 
  GROUP BY s_id
) as t
join Student as st
  on t.s_id = st.s_id 
```
意思：就是查询有成绩的学生的信息  
思路：
1. 查询成绩表
2. 按学生ID 分组
3. 获取学生ID 及总成绩（为啥查总成绩：因为按学生ID 分组后，每个学生对应好几个成绩。）
4. 将得到的信息重命名为t
5. t 表和 Student 表join。
6. 获取 s_id,  s_name, s_birth, s_sex, s_score  

## 查询在 Score 表不存在成绩的学生信息
```sql
SELECT  
  st.s_id as s_id,
  st.s_name as s_name,
  st.s_birth as s_birth,
  st.s_sex as s_sex,
  sc.s_score as s_score
FROM Score as sc
RIGHT JOIN Student as st
  on sc.s_id = st.s_id
WHERE sc.s_score IS null 
```
思路：查所有学生中成绩信息，排除有成绩的学生信息。
1. 查询所有学生的信息，及对应的学生成绩。（right join 以右表 Student表为主。不论成绩表是否有成绩，有一个学生就显示一个成绩。如果没有成绩，会显示为空null）
2. 当成绩表中 s_score 为 null 时
3. 获取s_id, s_name, s_birth, s_sex, s_score 字段信息。


查询所有同学的学生编号、学生姓名、选课总数、所有课程的总成绩(没成绩的显示为 null )

4.1 查有成绩的学生信息

查询「李」姓老师的数量

查询学过「张三」老师授课的同学的信息

查询没有学全所有课程的同学的信息

查询至少有一门课与学号为" 01 "的同学所学相同的同学的信息

查询和" 01 "号的同学学习的课程 完全相同的其他同学的信息

查询没学过"张三"老师讲授的任一门课程的学生姓名

查询两门及其以上不及格课程的同学的学号，姓名及其平均成绩

检索" 01 "课程分数小于 60，按分数降序排列的学生信息

按平均成绩从高到低显示所有学生的所有课程的成绩以及平均成绩

### 查询各科成绩最高分、最低分和平均分
```sql

```

以如下形式显示：课程 ID，课程 name，最高分，最低分，平均分，及格率，中等率，优良率，优秀率

及格为>=60，中等为：70-80，优良为：80-90，优秀为：>=90

要求输出课程号和选修人数，查询结果按人数降序排列，若人数相同，按课程号升序排列

按各科成绩进行排序，并显示排名， Score 重复时保留名次空缺
15.1 按各科成绩进行排序，并显示排名， Score 重复时合并名次

查询学生的总成绩，并进行排名，总分重复时保留名次空缺
16.1 查询学生的总成绩，并进行排名，总分重复时不保留名次空缺

统计各科成绩各分数段人数：课程编号，课程名称，[100-85]，[85-70]，[70-60]，[60-0] 及所占百分比

查询各科成绩前三名的记录

查询每门课程被选修的学生数

查询出只选修两门课程的学生学号和姓名

查询男生、女生人数

查询名字中含有「风」字的学生信息

查询同名同性学生名单，并统计同名人数

查询 1990 年出生的学生名单

查询每门课程的平均成绩，结果按平均成绩降序排列，平均成绩相同时，按课程编号升序排列

查询平均成绩大于等于 85 的所有学生的学号、姓名和平均成绩

查询课程名称为「数学」，且分数低于 60 的学生姓名和分数

查询所有学生的课程及分数情况（存在学生没成绩，没选课的情况）

查询任何一门课程成绩在 70 分以上的姓名、课程名称和分数

查询不及格的课程

查询课程编号为 01 且课程成绩在 80 分以上的学生的学号和姓名

求每门课程的学生人数

成绩不重复，查询选修「张三」老师所授课程的学生中，成绩最高的学生信息及其成绩

成绩有重复的情况下，查询选修「张三」老师所授课程的学生中，成绩最高的学生信息及其成绩

查询不同课程成绩相同的学生的学生编号、课程编号、学生成绩

查询每门功成绩最好的前两名

统计每门课程的学生选修人数（超过 5 人的课程才统计）。

检索至少选修两门课程的学生学号

查询选修了全部课程的学生信息

查询各学生的年龄，只按年份来算

按照出生日期来算，当前月日 < 出生年月的月日则，年龄减一

查询本周过生日的学生

查询下周过生日的学生

查询本月过生日的学生

查询下月过生日的学生

## 参考
https://www.jianshu.com/p/476b52ee4f1b