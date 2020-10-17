# 数据库01mysql常用操作速查
## 启动停止服务和设置
net start MySQL服务名  
net stop MySQL服务名  

mysql -h主机名 -u用户名 \[-P端口\] -p  
quit;或exit;  

set names utf8;  
mysqldump -u用户名 -p 数据库名>文件名(备份）  
mysql -u用户名 -p 数据库名<文件名(还原数据库)  

查看最大连接数:  
show variables like ‘%max_connections%’;  
修改最大连接数  
方法一：修改配置文件。打开MySQL配置文件 my.ini 或 my.cnf查找 max_connections=100 ,重起MySQL.  
方法二：命令行修改.命令行登录MySQL后。set global max_connections=200;只在MySQL当前服务进程有效，一旦重启，会恢复到初始
## 数据库
状态.  
show databases;  

use 数据库名;  
create datebase 数据库名;  
drop database 数据库名;  

select database();(显示当前正在使用的数据库)  
## 数据表
create table 数据表名称 (字段名1 字段名1数据类型,字段名2 字段名2数据类型,...,字段名n 字段名n数据类型);  
show tables;  
desc 数据表名称;  
drop table 数据表名称;  

alter table 要修改的数据表名称 rename 修改后的数据表名称;  
alter table 数据表名称 modify 字段名 字段名数据类型;(修改字段类型)  
alter table 数据表名称 change 要修改的字段名 修改后的字段名 修改后的字段名数据类型;(字段名称并且修改其类型)  
alter table 数据表名称 drop 字段名;  
alter table 数据表名称 add 字段名 字段名数据类型;  

insert into 数据表名称 values (字段1的值,字段2的值,...,字段n的值);  
说明:auto_increment输入NULL字段值序号自增.字符类型,枚举类型,日期加单引号.  

select * from 数据表名称;  
select 字段名 from 数据表名称;  

delete from 数据表名称;  
delete from 数据表名称 where 字段名 = '关键字';  

update 数据表名称 字段名1 = 修改值 where 字段名2 = '关键字';  

根据已有的表创建新表：  
A：create table tab_new like tab_old (使用旧表B创建新表A)   
 备注：此种方式在将表B复制到A时候会将表B完整的字段结构和索引复制到表A中来  
B：create table tab_new as select col1,col2… from tab_old definition only  
 备注：此种方式只会将表B的字段结构复制到表A中来，但不会复制表B中的索引到表A中来。这种方式比较灵活可以在复制原表表结构的同时指定要复制哪些字段，并且自身复制表也可以根据需要增加字段结构。  
结论：  
create table as select 会将原表中的数据完整复制一份，但表结构中的索引会丢失。  
create table like 只会完整复制原表的建表语句，但不会复制数据  
两种方式在复制表的时候均不会复制权限对表的设置。比如说原本对表B做了权限设置，复制后，表A不具备类似于表B的权限。   

添加主键： Alter table tabname add primary key(col)  
说明：删除主键： Alter table tabname drop primary key  
备注：一个数据表只可以有一个主键，所以不存在删除某一列的主键。  

创建测试表  
```
CREATE TABLE `test_number` (
  `id` int(10) unsigned NOT NULL AUTO_INCREMENT,
  `number` int(11) unsigned NOT NULL DEFAULT '0' COMMENT '数字',
  PRIMARY KEY (`id`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8
```

视图  
```
# 查看创建视图语句
SHOW CREATE VIEW v;
# 更改视图
CREATE OR REPLACE VIEW v AS SELECT name, age FROM n;
ALTER VIEW v AS SELECT name FROM n ;
# 删除视图
DROP VIEW IF EXISTS v;
```

其他  
```
# 数据库备份
mysqldump -u root -p db_name > file.sql
mysqldump -u root -p db_name table_name > file.sql
# 数据库还原
mysql -u root -p < C:\file.sql
```

## 导入导出
```
select * from train\_user\_bt  into outfile 'd:/test.csv'  
fields terminated by ','  
lines terminated by '\\r\\n';  
```
## 进阶查询
几个高级查询运算词  
A： UNION 运算符  
UNION 运算符通过组合其他两个结果表（例如 TABLE1 和 TABLE2）并消去表中任何重复行而派生出一个结果表。当 ALL 随 UNION 一起使用时（即 UNION ALL），不消除重复行。两种情况下，派生表的每一行不是来自 TABLE1 就是来自 TABLE2。
B： EXCEPT 运算符  
EXCEPT 运算符通过包括所有在 TABLE1 中但不在 TABLE2 中的行并消除所有重复行而派生出一个结果表。当 ALL 随 EXCEPT 一起使用时 (EXCEPT ALL)，不消除重复行。  
C： INTERSECT 运算符  
INTERSECT 运算符通过只包括 TABLE1 和 TABLE2 中都有的行并消除所有重复行而派生出一个结果表。当 ALL 随 INTERSECT 一起使用时 (INTERSECT ALL)，不消除重复行。  
注：使用运算词的几个查询结果行必须是一致的。  


## truncate与delete的区别
a.事务：truncate是不可以rollback的，但是delete是可以rollback的；  
原因：truncate删除整表数据(ddl语句,隐式提交)，delete是一行一行的删除，可以rollback  
b.效果：truncate删除后将重新水平线和索引(id从零开始) ,delete不会删除索引  
c.truncate 不能触发任何Delete触发器。  
d.delete 删除可以返回行数  

## 参考
MySQL SQL命令速查：[https://segmentfault.com/a/1190000007507732](https://segmentfault.com/a/1190000007507732)  
MySQL 速查手册：[http://blog.chinaunix.net/attachment/attach/26/22/61/0626226106fb9b53d3cb8bd8d6ad96f0d50618092e.pdf](http://blog.chinaunix.net/attachment/attach/26/22/61/0626226106fb9b53d3cb8bd8d6ad96f0d50618092e.pdf)  
mysql sql常用语句大全：https://www.cnblogs.com/cy568searchx/p/3747993.html  
row_number() over partition by 分组聚合：https://www.cnblogs.com/starzy/p/11146156.html  
MYSQL基础常见常用语句200条:https://blog.csdn.net/c361604199/article/details/79479398  
MySQL必备的常见知识点汇总整理(实例,事务acid,事务隔离讲解较好等):https://www.jb51.net/article/185982.htm  
