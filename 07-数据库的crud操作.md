# 第七章 数据库的crud操作
## 7.1 基本概念 
> crud操作，就是表的增删改查
## 7.2 insert语句
> 基本的概念就是将数据添加到某个表中
+ 基本语法
```sql
  insert into tablename [(colum)] values (value1,value2);
```
+ 说明
1. 添加数据时，可以一条insert语句可以添加多条记录
2. 在添加时，可以指定字段名称
+ 快速入门案例
```sql
  create table `goods`(
    id int not null default 1 comment 'id号',
    goods_name varchar(64) not null default '',
    price float not null default 0
  )charset=utf8 engine=myisam;
  
  // 添加两条记录
  insert into `goods` values(1,'可口可乐',12.98);
  insert into `goods` values(2,'百世可乐',13.33);
```
+ 细节说明
1. 插入的数据应与字段的数据类型相同
2. 数据的大小应在字段规定的范围内
3. 在values中列出的数据位置必须与被加入的列排列位置相对应
4. 字符和日期型数据应包含在单引号中
5. 插入空值（前提是该字段允许为空值）
6. insert int (),()形式添加多条记录
7. 如果是表中的所有字段添加数据，可以不写前面的字段名称，如果你只是给部分字段添加值，则必须指定列名
```sql
  insert into `goods2`(id,goods_name) values(30,'cc');
```
