# SQL 学习  
## 查询学生的姓名  
```
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
```
select COUNT(t_id) as 'li_t_cnt' from Teacher where t_name like '李%'
 
输出:
li_t_cnt
1
```

## 查询男生、女生人数   
```
select s_sex, count(s_sex) as 人数 from Student GROUP BY s_sex

```
## 查询1990年出生的学生名单  
```
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
```
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
```
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
```
select  
		s.s_id as s_id,   
		s.c_id as c_id,   
		s.s_score as s_score,   
		c.c_name as c_name,   
		c.t_id as t_id  
```
将字段名重命名（为了怕出错，有重复字段），获取s_id、c_id、s_score、c_name、t_id字段信息。  
### 第三步：
```    
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
```
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
```
select c_id, count(c_id) from Score where s_score > 60 group by c_id  
```

