---
title: MySql 基础
date: 2020-07-12 22:15:22
tags: MySql
---

# Mysql

## sql 分类

### 1、DDL

```mysql
CREATE //创建
DROP //删除
ALTER //修改
```

### 2、DML

```mysql
INSERT //插入
UPDATE //修改
DELETE //删除
```

### 3、DQL

```mysql
SELECT //查询
```

<!-- more -->

### 4、DCL

```mysql
GRANT //赋权
REMOTE //减权
```

### 5、DTL
mysql
```
BEGIN //开始
COMMIT //提交
ROLLBACK //回滚
```

## 命令行操作数据库

### 链接数据库

```mysql
mysql -uroot -p
Enter password:******

mysql -uroot -p******
```

### 结束当前命令

```mysql
\c
```

### 查看帮助命令

```mysql
\h
```

### 相当于分号

```mysql
\g
```

### 退出数据库

```mysql
exit;
```

### 查看数据库

```mysql
show databases;
```

### 创建库

```mysql
create database dbName;
```

注：建库必须设置字符集

```mysql
default charset utf8 collate utf8_general_ci
```

### 查看建库语句

```mysql
show create database dbName;
```

### 删除库

```mysql
drop database dbName;
```

### 使用库

```mysql
use dbName;
```

### 查看当前库下有那些表

```mysql
show tables;
```

### 创建表

```mysql
create table tName (field1 type(length), field2 type(length), field3 type(length)...)  //field 字段，type 类型，length 长度
```

注：建表必须指定引擎

```mysql
ENGINE=InnoDB
```

### 查看建表语句

```mysql
show create table tName;
```

### 删除表

```mysql
drop table tName;
```

### 修改表

```mysql
alter table tName rename newTName;
```

### 查看表结构

```mysql
desc tName;
```

### 添加表字段

```mysql
alter table tName add addField type after field; //添加在field之后

alter table tName add addField type first; //添加在第一个
```

### 删除表字段

```mysql
alter table tName drop field;
```

### 修改表字段的数据类型

```mysql
alter table tName modify field type;
```

### 修改表字段的名字和数据类型

```mysql
alter table tName change oldField newField newType;
```

### 查看引擎

```mysql
show engines;
```

### 导出库

```mysql
mysqldump -uroot -p dbName > path //path 文件路径和文件名
```

注：该命令需要退出mysql

### 导出表

```mysql
mysqldump -uroot -p dbName tName > path
```

### 导入

```mysql
mysql -uroot -p newDbName < path
```

sql脚本数据量较大时，导入时推荐使用：

```mysql
source path
```



source 命令需要进入数据库，选择表

## MyISAM 和 InnoDB 的区别

### MyISAM

不支持事务

不支持外键

读取速度快

增删改为表锁

### InnoDB

支持事务

支持外键

写入速度快

增删改为行锁

## char 和 varchar 的区别

### char

固定长度

最多存放255个字符

速度快

### varchar

可变长度

最多存放65532个字符

速度慢

## 索引

### 普通索引

```mysql
alter table tName add index(field);
```

### 唯一索引

```mysql
alter table tName add unique(field);
```

### 主键索引

```mysql
alter table tName add primary key(field);
```

### 全文索引

```mysql
alter table tName add fulltext(field);
```

## 增删改查

### 增加数据

如下必须和字段一一对应，包括id

```mysql
insert into tName values (value1, value2, value3...);
```

这种需要和前面的字段对应

```mysql
insert into (field1, field2, field3...) values (value1, value2, value3...);
```

### 删除数据

```mysql
delete from tName where id=1; //单行删除

delete from tName where id in (1, 2, 3...); //批量删除
```

### 修改数据

```mysql
update tName set field1=value1, field2=value2... where id=1;

update tName set intField=intField+10 where id=1;

update tName1 as t1, tName2 as t2 set t1.field1=value1, t2.field2=value2 where t1.id = t2.uid and t1.id=1;
```

### 查询数据

#### 1、查询所有

```mysql
select * from tName; //一般不用，速度慢
```

#### 2、按照指定字段查询

查询field1, field2字段在tName表下所有的值

```mysql
select field1, field2... from tName;
```

#### 3、去除重复值 distinct

查询tName表下field1字段的值如果有重复值，只保留一个

```mysql
select distinct field1 from tName;
```

#### 4、配合where查询

查询field1, field2字段在tName表下id为1的值

```mysql
select field1, field2... from tName where id=1;
```

#### 5、between and

查询field1, field2字段在tName表下money的值在100和500之间的值

```mysql
select field1, field2... from tName where money between 100 and 500;

select field1, field2... from tName where money >= 100 and money<=500;
```

#### 6、> < >= <= != <>

!= 和 <> 相同

```mysql
select field1, field2... from tName where money > 100;

select field1, field2... from tName where money < 100;

select field1, field2... from tName where money >= 100;

select field1, field2... from tName where money <= 100;

select field1, field2... from tName where money != 100;

select field1, field2... from tName where money <> 100;
```

#### 7、 in

查询field1, field2字段在tName表下id等于1，2，3

```mysql
select field1, field2... from tName where id in (1, 2, 3...);
```

#### 8、模糊查询 like

查询field1, field2字段在tName表下province字段含有“安”字的

```mysql
select field1, field2... from tName where province like '%安%';
```

查询field1, field2字段在tName表下province字段“安”字开头的

```mysql
select field1, field2... from tName where province like '安%';
```

查询field1, field2字段在tName表下province字段“安”字结尾的

```mysql
select field1, field2... from tName where province like '%安';
```

查询field1, field2字段在tName表下province字段“安”字开头并且只有两个字的

```mysql
select field1, field2... from tName where province like '安_';
```

查询field1, field2字段在tName表下province字段“安”字结尾并且只有两个字的

```mysql
select field1, field2... from tName where province like '_安';
```

注：

使用索引可能会导致模糊查询失败

#### 9、排序 order by

查询field1, field2字段在tName表下按照id升序

```mysql
select field1, field2... from tName order by id
```

查询field1, field2字段在tName表下按照id降序

```mysql
select field1, field2... from tName order by id desc
```

#### 10、分页 limit

查询field1, field2字段在tName表下1-5条数据

```mysql
select field1, field2... from tName limit 0, 5  //0，5 从第一行开始取，取5行数据
```

注：

当前页为n，当前页显示5条数据，limit条件为 `limit (n-1) * 5 , 5`

#### 11、分组 group by

查询field1, field2字段在tName表下根据province分组

```mysql
select field1, field2... from tName group by province;
```

#### 12、求和 count

查询tName表下的数据行数

```mysql
select count(*) from tName;
```

查询province的内容和province对应的行数

```mysql
select province, count(province) from tName group by province;
```

#### 13、别名 as

这样查询后的结果集只有province，pCount两个字段

```mysql
select province, count(province) as pCount from tName group by province;
```

#### 14、求最大值 max

求intField字段在tName表下的最大值

```mysql
SELECT max(intField) FROM tName;
```

#### 15、求最小值 min

求intField字段在tName表下的最小值

```mysql
SELECT min(intField) FROM tName;
```

#### 16、求平均值 avg

求intField字段在tName表下的平均值

```mysql
SELECT avg(intField) FROM tName;
```

#### 17、对结果集进行再次过滤 having

求province，pCount字段在tName表下并且pCount>=2

```mysql
select province, count(province) as pCount from tName group by province having pCount>=2;
```

### 多表查询

#### 1、隐式内连接

查询tName1表下的field2字段和tName2表下的field2字段，二者的关联条件为tName1.field1和tName2.field1相等,取出来的值必须在两个表里都能够对应，取交集

```mysql
select tName1.field2, tName2.field2 from tName1, tName2 where tName1.field1=tName2.field1;
```

#### 2、显式内连接 inner join on

效果和隐式内连接相同

```mysql
select tName1.field2, tName2.field2 from tName1 inner join tName2 on tName1.field1=tName2.field1;
```

#### 3、左连接 left join on

查询tName1表下的field2字段和tName2表下的field2字段，二者的关联条件为tName1.field1和tName2.field1相等,取出来的值以tName1为主，tName2中如果没有值会为null

```mysql
select tName1.field2, tName2.field2 from tName1 left join tName2 on tName1.field1=tName2.field1;
```

#### 4、右连接 right jion on

查询tName1表下的field2字段和tName2表下的field2字段，二者的关联条件为tName1.field1和tName2.field1相等,取出来的值以tName2为主，tName1中如果没有值会为null

```mysql
select tName1.field2, tName2.field2 from tName1 right join tName2 on tName1.field1=tName2.field1;
```

#### 5、嵌套查询

```mysql
select field from tName1 where id in (select id from tName2);  //一般不用，效率低
```

## 事务

更新多个表，全部更新成功才是成功，只要有一个更新失败就是失败

全部成功提交更新结果

失败就回滚

注：

数据库引擎必须是InnoDB

### 1、把自动提交改成手动提交

```mysql
set autocommit = 0;
```

### 2、开启一个事务

```mysql
begin
```

### 3、执行多个sql语句

```mysql
insert into tName values (value1, value2, value3...);
delete from tName where id=1;
```

### 4、提交或回滚

```mysql
commit/rollback
```