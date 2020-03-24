# 第十四章 mysql自增长
## 基本概念
> 在某张表中，存在一个id列(整数),我们希望在添加记录的时候，该列从1开始，自动的增长。
## 基本语法
> 字段名称 字段类型（整形） primary key auto_increment
## 举例说明
```sql
	create table `user22`(
		id int primary key auto_increment,
		name varchar(32) not null default ''
	)charset=utf8 engine=myisam;

	insert into user22 values(null,'hello');
```
## 自增长的注意事项
1. 一般来说自增长要配合primary key 来使用
2. 自增长也可以单独使用（但是需要配合一个unique）
```sql
  create table `user23`(
    id int unique auto_increment,
    name varchar(32) not null default ''	
  )charset=utf8 engine=myisam;

  insert into user23 values(null,'刘备');
  insert into user23 values(null,'张飞');
```
3. 自增长修饰的字段为整数型的(虽然小数也可以但是非常非常少这样使用)
4. 自增长默认从 1开始，你也可以通过如下命令修改alter table 表名 auto_increment = xxx;
```sql
  alter table user23 auto_increment = 100;
```
+ 说明
> 可以通过alter table 表名 auto_increment = 值，来修改我们的自增长的值  
> 我们也可以直接指定自增长的字段  
```sql
  insert into user23 values(3,'你好');
```
> 自增长规则是（即你不指定值），找到该字段的最大值+1
