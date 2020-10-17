# 数据库04sqlite转mysql
基本思路:sqlite导出sql保存到mysql  

1,sqlite导出sql,手工替换  
2,sqlite导出sql,脚本替换  
3,makemigrations生成mysql表，数据:sqlite=>csv=>mysql  
4,django主从数据库机制，将sqlite同步到mysql  

## sqlite导出sql
```
sqlite3 database.sqlite3
sqlite3> .output /path/to/dump.sql
sqlite3> .dump
sqlite3> .exit
```
or  
```
sqlite3 database.sqlite3 .dump
```

## 01,sqlite=>mysql语句修改  
为了保证SQL语句的兼容，需要将SQLite的特有的格式，修改为MySQL的格式。下面为我总结的一般规则(下面的方括号应被忽略)：  
```
将 ["] 改为 [`]
也可以移除全部的 ["] ，但是如果有一些函数名作为字段名(e.g. regexp)时将会遇到错误
需要注意一些默认为 ["] ，其作用不在字段上的，不应被替换而应当被保留
移除所有的 [BEGIN TRANSACTION] [COMMIT] 以及 任何包含 [sqlite_sequence] 的(整)行
将所有 [autoincrement] 改为 [auto_increment]
将所有 ['f'] 改为 ['0'] 并将所有 ['t'] 改为 ['1']
或者['False']改为['0']及['True']改为['1']
text字段不能设置unipue，需改为varchar(255)
注意sqlite是不区分类型的，所以有些整形字段和text字段要修改配合mysql。
默认设为CURRENT_TIMESTAMP的字段类型一定为TIMESTAMP。
```
将修改完的文件保存。   
放弃，太麻烦且容易错，无法复用。  
## 02,sqlite转mysql格式脚本
01，https://github.com/vwbusguy/sqlite-to-mysql  
02，（包含2不同脚本）Quick easy way to migrate SQLite3 to MySQL? [closed]:https://stackoverflow.com/questions/18671/quick-easy-way-to-migrate-sqlite3-to-mysql  

以上脚本收集整合到github[sqlite-to-mysql](https://github.com/yuanjh6/sqlite-to-mysql/)  
可惜的是没有一个可以直接使用的，简单的表可以搞定，一旦涉及复杂的字段，比如datetime等，就会踩坑。  
放弃!  


## 03,makemigrations方案
django makemigrations生成mysql表，数据:sqlite=>csv=>mysql  
前半部分可以:makemigrations在mysql创建新表  
后半部分不行:csv数据sqlite导出导入mysql，失败。django支持利用json导出导入数据，也失败。  

Django2 SQLite3迁移到MySQL数据库:https://www.jianshu.com/p/6b28f7dbc1fa  
django sqlite数据库导出迁移到mysql数据库方法:https://blog.csdn.net/YPL_ZML/article/details/91892306  


## 04,django主从数据库机制  
主要参考:  
django sqlite3迁移到mysql:www.voidcn.com/article/p-hesvaooz-ru.html  
数据表创建成功，但迁移数据时失败。  
思路上查询出对象在保存到从数据库是没问题的，实际上由于数据存在主键依赖，导致创建表或记录的前后次序有特定要求，自动查询的化未必符合这种要求。  
另一个问题就是这种方案会对django的默认数据表也进行迁移，也会产生主键依赖问题。  


## 总结
没找到合适方案可以简单快速的迁移，最后采用03方法，django在mysql生成新表。由于目前测试数据并不多，所以数据由各个模块开发人员自行初始化。  

## 参考
将sqlite3中数据导入到mysql中的实战教程:https://www.jb51.net/article/118379.htm  
sqlite导入到mysql:https://www.cnblogs.com/goldenstones/articles/9185950.html  

