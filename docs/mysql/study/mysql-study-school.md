# SQL 学习  
## 查询学生的姓名  
```sql
select s_name FROM Student 

输出：  
s_name
赵雷
钱电
孙风
李云
周梅
吴兰
郑竹
王菊
```

## 查询“李”姓老师的数量  
```sql
select COUNT(t_id) as 'li_t_cnt' from Teacher where t_name like '李%'
 
输出:
li_t_cnt
1
```

## 查询男生、女生人数   
```sql
select s_sex, count(s_sex) as 人数 from Student GROUP BY s_sex

```
## 查询1990年出生的学生名单  
```sql
select s_name FROM Student WHERE s_birth like '1990%'  

输出：
s_name  
赵雷  
钱电  
孙风  
李云  
王菊  

```
## 查询每门课程的学生人数  
```sql
SELECT c_id, COUNT(s_id) as 人数 
FROM (
	select 
	  s.s_id as s_id,
	  s.c_id as c_id,
	  s.s_score as s_score,
	  c.c_name as c_name,
	  c.t_id as t_id
FROM Score as s
join Course as c
on s.c_id = c.c_id
) as t
GROUP BY c_id

输出：
c_id
01
02
03
人数
6
6
6
```
### 注意：子查询（当一个查询是另一个查询的条件时，称之为子查询。） join的表需要重新命名，如果不命名会出现错误
``` 
Every derived table must have its own alias

# -每个派生表都必须有自己的别名
```

## 查询每门课程成绩大于60的数量  
```sql
select c_id, c_name, count(c_id)
from (
	select 
		s.s_id as s_id, 
		s.c_id as c_id, 
		s.s_score as s_score, 
		c.c_name as c_name, 
		c.t_id as t_id
	from Score as s
	join Course as c
	on s.c_id = c.c_id
) as t
where s_score > 60
group by c_id

```
### 第一步：
from Score as s  
join Course as c  
on s.c_id = c.c_id  
from-查询，as-重命名（字段名太长，重命名之后简单化，或者两个字段冲突需要重命名） 查询 Score 表，查询 Course 表，当 S 表c_id和 C 表c_id 一样是将两张表信息关联起来
### 第二步： 
```sql
select  
  s.s_id as s_id,   
  s.c_id as c_id,   
  s.s_score as s_score,   
  c.c_name as c_name,   
  c.t_id as t_id  
```
将字段名重命名（为了怕出错，有重复字段），获取s_id、c_id、s_score、c_name、t_id字段信息。  
### 第三步：
``` sql   
from (  
	select   
	  s.s_id as s_id,   
	  s.c_id as c_id,   
	  s.s_score as s_score,   
	  c.c_name as c_name,   
	  c.t_id as t_id  
	from Score as s  
	join Course as c  
	on s.c_id = c.c_id  
) as t  
where s_score > 60  
```
将join得到的表，临时命名为t，查询表 t 中成绩大于60的字段。  

### 第四步： 
```  
group by c_id 
``` 
按照 c_id 分组  

### 第五步：
```
select c_id, c_name, count(c_id)
```
获取 c_id, c_name, count(c_id)字段信息。

### 其他查询方法1：
```sql
select   
	a.c_id as c_id,  
	b.c_name as c_name,  
  a.c_cnt as c_cnt  
from (  
	select c_id, count(c_id) as c_cnt from Score where s_score > 60 group by c_id  
) as a  
left join Course as b  
on a.c_id = b.c_id   
```
### 其他查询方法2：  
```sql
select c_id, count(c_id) from Score where s_score > 60 group by c_id  
```
## 查询所有同学的学生编号、学生姓名、选课总数、所有课程的总成绩
分析：每个同学的编号，每个同学的姓名，每个同学的选课总数，每个同学的总成绩。
```sql
SELECT sc.s_id, st.s_name, COUNT(sc.c_id),SUM(sc.s_score)
from Score as sc
JOIN Student as st
on sc.s_id = st.s_id
GROUP BY sc.s_id,st.s_name
```
1. from Score as sc   
查询成绩表，重命名为sc  

2. join Student as st  
Score成绩表与Student学生表关联  

3. GROUP BY sc.s_id,st.s_name  
按照成绩表的学生ID和学生表的学生姓名分组。学生ID和姓名为一一对应关系。

4. SELECT sc.s_id, st.s_name, COUNT(sc.c_id),SUM(sc.s_score)  
获取sc.s_id, st.s_name, COUNT(sc.c_id),SUM(sc.s_score) 每个字段信息。

### 第二种写法：先算出结果，后join姓名。
```sql
select 
  t.s_id as s_id, 
  st.s_name as s_name, 
  t.s_c_cnt as s_c_cnt, 
  t.s_c_sum as s_c_sum
from (
  SELECT 
    sc.s_id as s_id, 
    COUNT(sc.c_id) as s_c_cnt, 
    SUM(sc.s_score) as s_c_sum
  FROM Score as sc
  GROUP BY sc.s_id
) as t
join Student as st
on t.s_id = st.s_id
```
## 查询平均成绩大于等于60分的同学的学生编号和学生姓名和平均成绩
```sql
SELECT
	sc.s_id as s_id, 
	st.s_name, 
	ROUND(AVG(sc.s_score),2) as s_score
FROM Score as sc
JOIN Student as st
	on sc.s_id = st.s_id 
GROUP BY st.s_name, sc.s_id 
HAVING ROUND(AVG(sc.s_score), 2) >= 60 
ORDER BY sc.s_id
```
1. FROM Score as sc  
JOIN Student as st  
查询 Score 和 Student 表  

2. JOIN Student as st  
	on sc.s_id = st.s_id   
当sc.s_id 相等于 st.s_id 相等时，join 表Score 和 表Student，并且重命名为sc,st。

3. GROUP BY st.s_name, sc.s_id   
按st表姓名及sc表学生ID（一对一关系） 分组。

4. HAVING ROUND(AVG(sc.s_score), 2) >= 60   
注意：HAVING语句通常与GROUP BY语句联合使用，用来过滤由GROUP BY语句返回的记录集。筛选出成绩平均分大于等于60的。

5. SELECT  
	sc.s_id as s_id,   
	st.s_name,   
	ROUND(AVG(sc.s_score),2) as s_score  
获取 sc.s_id， st.s_name, ROUND(AVG(sc.s_score),2)   

6. ORDER BY sc.s_id  
对 sc.s_id 排序（默认正序）  

## 查询学过"张三"老师授课的同学的信息
```sql
SELECT st.*
FROM Student as st
JOIN Score as sc
on st.s_id = sc.s_id
WHERE sc.c_id 
in (
SELECT c_id FROM Course WHERE t_id =(
SELECT t_id from Teacher WHERE t_name = '张三'))
```
1. 
```
SELECT st.*  
FROM Student as st  
JOIN Score as sc  
on st.s_id = sc.s_id  
与
 SELECT c_id FROM Course WHERE t_id =(  
SELECT t_id from Teacher WHERE t_name = '张三')  
同时进行，查询获取
```
2. 
```
WHERE sc.c_id   
in (  
当 sc.c_id 与 in 里的字段相同时，得出结果
```
注意：IN 操作符允许我们在 WHERE 子句中规定多个值  
```
SELECT column_name(s)
FROM table_name
WHERE column_name 
IN (value1,value2,...)
```

### 我写的（最差的写法）
想法：表Student 和 表Score先join，取出其中所要字段，然后和 表Course join，取出其中需要字段，最后和 表Teacher join，获取需要字段，加上需要的条件。
```sql
SELECT 
	-- te.t_name,
  f.s_id, 
	f.s_name,
	f.s_birth,
	f.s_sex, 
	f.c_id,
	f.s_score 
FROM(
SELECT  
  cor.t_id, 
	t.s_id, 
	t.s_name,
	t.s_birth,
	t.s_sex, 
	t.c_id,
	t.s_score 
from (
SELECT 
	sc.s_id as s_id, 
	st.s_name as s_name,
	st.s_birth as s_birth,
	st.s_sex as s_sex,
	sc.c_id as c_id,
	sc.s_score as s_score 
FROM Student as st
JOIN Score as sc
on st.s_id = sc.s_id
)as t
join Course as cor
on cor.c_id = t.c_id
)as f
join Teacher as te
on te.t_id = f.t_id
WHERE te.t_name = '张三'
```

### 王扬庭写的

1. 第一种写法(最好写法)
想法：直接join四张表，逻辑强，不会混乱。  
```sql
select 
	st.s_id, 
	st.s_name,
	st.s_birth,
	st.s_sex, 
	co.c_name,
	sc.s_score 
from Student as st
join Score as sc
	on st.s_id = sc.s_id
join Course as co
	on co.c_id = sc.c_id
join Teacher as te
  on te.t_id = co.t_id
where te.t_name = '张三'
```

2. 第二种写法（逻辑不是很清楚）
```sql
select 
	st.s_id, 
	st.s_name,
	st.s_birth,
	st.s_sex, 
	co.c_name,
	sc.s_score 
from Student as st, Score as sc, Course as co, Teacher as te
where st.s_id = sc.s_id 
	and co.c_id = sc.c_id
	and	te.t_id = co.t_id
  and te.t_name = '张三'
```
## 查询没学过"张三"老师授课的同学的信息

```sql
select 
	st.s_id, 
	st.s_name,
	st.s_birth,
	st.s_sex, 
	co.c_name,
	sc.s_score 
from Student as st
join Score as sc
	on st.s_id = sc.s_id
join Course as co
	on co.c_id = sc.c_id
join Teacher as te
  on te.t_id = co.t_id
where te.t_name != '张三'
```