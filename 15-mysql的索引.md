# 第十五章 mysql的索引
## 15.1 基本介绍
1. 索引的作用是提供数据的检索/查询速度
2. 索引对我们的添加，修改，删除有影响，但是几乎可以忽略
## 15.2 索引的工作原理
> 因为我们创建了索引，提高的检索速度，但是，对insert ,update , delete 有小的影响.原因是当有索引后，我们对表进行增，删除，修改时，还要对索引进行维护工作。
## 15.3 索引的类型
1. 主键索引（在字段上创建了主键，该列就是主键索引）
2. 唯一索引（在字段上创建了unique，该列就是唯一索引）
3. 普通索引（在某个字段上，使用create index 指令，这个列就是一个普通索引）
4. 全文索引（用于检索某个字段的关键字，比如查询文章的关键字）
## 15.4 索引的管理
### 索引的创建
#### 主键索引的创建
> 主键索引的创建有两种形式
1. 在创建表时，就直接指定主键索引
```sql
  create table `user27`(
    id int primary key,
    name varchar(32) not null default ''
  )charset=utf8 engine=myisam;

  create table `user28`(
    id int,
    name varchar(32) not null default '',
    primary key(id)
  )charset=utf8 engine=myisam;

```
2. 先创建表，再制定索引
```sql
  create table `user29`(
    id int,
    name varchar(32) not null default ''
  )charset=utf8 engine=myisam;
  
  alter table `user29` add primary key(id);
```
+ 主键索引的特点
1.	一张表最多只能有一个主键索引，但是该主键索引可以指向多列,即前面老师说的复合主键.
2.	主键索引的效率很高
3.	主键索引的值，不能为null, 也不能重复.
4.	主键索引一般来说在表的第一个字段上创建，该字段一般来说是整型，并且是 auto_incrment.
#### 唯一索引的创建
1. 在创建表时，就直接指定唯一索引
```sql
  create table `user30`(
    id int primary key auto_increment,
    email varchar(32) unique
  )charset=utf8 engine=myisam;
  
   create table `user30`(
    id int primary key auto_increment,
    email varchar(32),
    unique(email)
  )charset=utf8 engine=myisam;
```
2. 在创建表后，再指定唯一索引
```sql
  create table `user32`(
    id int primary key auto_increment,
    email varchar(32)
  )charset=utf8 engine=myisam;
  // 使用alter table
  alter table user32 add unique(email);
  // 使用create index
  create unique index inx_uni_email on user32(email);
```
+ 唯一索引的特点
1. 一个表中，可以有多个唯一索引.
2. 一个唯一索引，可以指向多列(多个字段)
3. 唯一索引的效率也很高
4. 唯一索引的值，是不能重复，如果唯一索引还规定了 not null , 则唯一索引的值还不能null
5. 如果我们不确定某个字段是否有重复的值，则不能使用唯一索引.
#### 普通索引
1. 在创建表时，就直接指定
```sql
  create table `user33`(
    id int primary key auto_increment,
    email varchar(32),
    name varchar(32) not null default '',
    index(name)
  )charset=utf8 engine=myisam;
```
2. 先创建表后，再指定
```sql
  create table `user34`(
    id int primary key auto_increment,
    email varchar(32),
    name varchar(32) not null default ''    
  )charset=utf8 engine=myisam;

  // 方式1
  alter table user32 add index(name);
  // 方式2
  create index inx_name on user34(name);
```
+ 普通索引的特点
1. 一个表中，可以有多个普通索引
2. 当我们字段有重复的数据时，我们就使用普通索引即可.
3. 在实际的开发中，普通索引使用的最多.
#### 全文索引
+ 基本介绍
> 当我们需要对某个字段的内容的关键字进行检索时，就考虑使用全文索引，比如：我们需要子啊一篇文章中，检索有没有某个关键字，则使用全文索引.
+ 说明
> mysql支持全文索引的，但是目前支持mysql5.5是不支持中文，表的存储引擎是myisam.==> 怎么办 ? ==> 以后使用专业的全文检索技术(coreseek , 就是shpinx的中文版， 迅搜技术(xunsearch))
+ 举例说明
```sql
  create table articles(
    id INT UNSIGNED AUTO_INCREMENT NOT NULL PRIMARY KEY,
    title VARCHAR(200),
    body TEXT,
    FULLTEXT (title,body)
  )engine=myisam;
      INSERT INTO articles (title,body) VALUES
     ('MySQL Tutorial','DBMS stands for DataBase ...'),
     ('How To Use MySQL Well','After you went through a ...'),
     ('Optimizing MySQL','In this tutorial we will show ...'),
     ('1001 MySQL Tricks','1. Never run mysqld as root. 2. ...'),
     ('MySQL vs. YourSQL','In the following database comparison ...'),
     ('MySQL Security','When configured properly, MySQL ...');
```
+ 如何使用全文索引
> 查询title 或者 body 含义 database的记录
```sql
  select * from articles where match(title,body) against('database');
```
+ 停止词，匹配概率
> 针对特别普通的词，就可能是停止词  
> mysql的全文检索是安装概率来检索
### 索引的查询（有5种方式）
1. show index from 表名\G
2. show indexes from 表名\G
3. show keys from 表名\G
4. desc 表名
5. show create table 表名;
### 索引的删除
> alter table 表名 drop index 索引名
## 15.5 创建索引的规则
> 在我们的实际开发中，什么的样的字段适合创建索引
1. 较频繁的作为查询条件字段应该创建索引
```sql
  select * from emp where empno = 1;
```
2. 唯一性太差的字段不适合单独创建索引，及时频繁作为查询条件
```sql
  select * from emp where sex = '男'
```
3. 更新非常频繁的字段不适合创建索引
```sql
  select * from emp where logincount = 1;
```
4. 不会出现在where子句中字段不该创建索引
