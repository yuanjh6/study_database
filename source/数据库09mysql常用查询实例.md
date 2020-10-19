# 数据库09mysql常用查询实例
## 查询统计结果中的前n条记录

```
 SELECT * ,(yw+sx+wy) AS total FROM tb_score ORDER BY (yw+sx+wy) DESC LIMIT 0,$num
```

## 按月查询统计数据,区间查询between and

```
SELECT * FROM tb_stu WHERE month(date) = between 1 and 3 ORDER BY date ;
```
注：SQL语言中提供了如下函数，利用这些函数可以很方便地实现按年、月、日进行查询

year(data):返回data表达式中的公元年分所对应的数值

month(data):返回data表达式中的月分所对应的数值

day(data):返回data表达式中的日期所对应的数值


## NOT与谓词进行组合条件的查询

```
(1)NOT BERWEEN … AND … 对介于起始值和终止值间的数据时行查询 可改成 <起始值 AND >终止值
(2)IS NOT NULL 对非空值进行查询
(3)IS NULL 对空值进行查询
(4)NOT IN 该式根据使用的关键字是包含在列表内还是排除在列表外，指定表达式的搜索，搜索表达式可以是常量或列名，而列名可以是一组常量，但更多情况下是子查询
```

## 多列数据分组统计

 多列数据分组统计与单列数据分组统计类似

```
SELECT *，SUM(字段1*字段2) AS (新字段1) FROM 表名 GROUP BY 字段 ORDER BY 新字段1 DESC
SELECT id,name,SUM(price*num) AS sumprice  FROM tb_price GROUP BY pid ORDER BY sumprice DESC
```
 注：group by语句后面一般为不是聚合函数的数列，即不是要分组的列


## 查询“c001”课程比“c002”课程成绩高的所有学生的学号；

```
select a.* from
 (select * from sc a where a.cno='c001') a,
 (select * from sc b where b.cno='c002') b
 where a.sno=b.sno and a.score > b.score;
```

## 查询平均成绩大于60 分的同学的学号和平均成绩；

```
select sno,avg(score) from sc  group by sno having avg(score)>60;
```
## 查询没学过“谌燕”老师课的同学的学号、姓名；

```
select * from student st where st.sno not in
(
 select distinct sno from sc s
 join course c on s.cno=c.cno
 join teacher t on c.tno=t.tno where tname='谌燕'
)

```
## 查询没有学全所有课的同学的学号、姓名；

```
select stu.sno,stu.sname,count(sc.cno) from student stu
 left join sc on stu.sno=sc.sno
 group by stu.sno,stu.sname
 having count(sc.cno)<(select count(distinct cno)from course)
```

## 按各科平均成绩从低到高和及格率的百分数从高到低顺序

```
select cno,avg(score),sum(case when score>=60 then 1 else 0 end)/count(*) as 及格率
from sc group by cno
order by avg(score) , 及格率 desc
```
## 查询各科成绩前三名的记录:(不考虑成绩并列情况)

```
select * from
(select sno,cno,score,row_number()over(partition by cno order by score desc) rn from sc)
where rn<4
```

mysql还有其他写法，通过求出极值再进行关联


复制代码

```
SELECT t.stuid, 
        t.stuname, 
        t.score, 
        t.classid 
FROM stugrade t 
where t.score = (SELECT max(tmp.score) from stugrade tmp where tmp.classid=t.classid)
```


## 查询两门以上不及格课程的同学的学号及其平均成绩

```
语句：select stuId,avg(ifnull(stuScore,0)) from score where stuId in (select stuId from score where stuScore <60 group by stuId having count(*) >2) group by stuId;
```

## 参考
23个MySQL常用查询语句：https://bbs.csdn.net/topics/390407669 
Mysql 常用SQL语句集锦:https://zhuanlan.zhihu.com/p/24327708

MySQL学生表、老师表、课程表和成绩表查询语句，全部亲测：https://blog.csdn.net/wq12310613/article/details/100705492

MySQL全方位练习（学生表 教师表 课程表 分数表）：https://www.cnblogs.com/mzhaox/p/11280234.html  