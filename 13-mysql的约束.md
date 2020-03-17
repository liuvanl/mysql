# 第十三章 mysql的约束
## 13.1 基本介绍
> 约束是用于维护数据的完整性, 所谓数据的完整性指的是，就是根据业务逻辑的需要，我们将表的某些字段设置成一种约束，从而保证数据的合理性和完整性，比如身份证编号就是唯一，我们就可以使用unique 这个约束.
## 13.2 mysql约束分类
1. 主键约束：primary key
2. 唯一约束：unique
3. not null
4. check：检查约束，很多数据库都有，但是mysql只支持语法，并没有实际作用
5. 外键约束：foreign key
## 13.3 主键约束
### 基本语法
> 字段名 字段类型 primary key
### 说明
1. 主键约束一般在类型之后
2. 一旦一个字段被设置为主键约束，则该字段不能重复，也不能为null
### 案例
```sql
  create table `user11`(
    id int primary key comment 'id',
    name varchar(64) not null default '',
    email varchar(64) not null default ''
  )charset=utf8 engine=myisam;
  insert into `user11` values(1,'bbb','bbb@souhu.com');
  
  ----主键不能null值
  insert into `user11` values(null,'ccc','ccc@sohu.com');
```
### 主键的使用细节说明
1. primary key 不但不能重复而且不能为null
2. 一张表只能有一个主键，但可以是复合主键
#### 举例
```sql
  create table `user12`(
    id int,
    name varchar(32) not null default '',
    email varchar(32) not null default '',
    primary key(id,name)
  )charset=utf8 engine=myisam;
  
  insert into `user12` values(1,'aaa','aaa@sohu.com');
  insert into `user12` values(1,'bbb','bbb@sohu.com');
  /*这里会报错*/
  insert into `user12` values(1,'aaa','bbb@sohu.com');
```
3. 一般来说一张表总有primary key，而且是整数类型的
4. 主键的指定方式有两种
+ 在字段后面直接定义
```sql
  create table `user13`(
    id int primary key comment '主键约束',
    name varchar(32) not null default '',
    email varchar(32) not null default ''
  )charset=utf8 engine=myisam;
```
+ 在字段定义完毕后，在后面写
```sql
  create table `user13`(
    id int comment '主键约束',
    name varchar(32) not null default '',
    email varchar(32) not null default '',
    primary key(id)
  )charset=utf8 engine=myisam;
```
5. 如果你有一个主键后，还希望其它字段达到not null并且unique的效果，则可以定义如下表
```sql
  create table `user14`(
    id int,
    name varchar(32) not null default '',
    email varchar(32) not null unique default '',
    primary key(id)
  )charset=utf8 engine=myisam;
```
## 13.4 unique约束（唯一约束）
> 当我们希望某个字段的值不能出现重复值时，我们就可以将该字段设置成unique.
### 基本语法
> 字段名 字段类型 unique
### unique细节
1. 如果我们将某个字段设置为 unique ,但是没有设置  not null, 那么该字段可以为null,并且可以有多个null
2. 如果你要某个字段不能重复，而且不能为 null, 我们可以这样定义字段字段名 字段类型 not null  unique
3. 一个表中，可以有多个unique约束
## 13.5 外键约束
### 基本介绍
> 用于定义主表和从表之间的关系: 外键约束要定义在从表上，主表则必须具有主键约束或是unique约束.,当定义外键约束后，要求外键列数据必须在主表的主键列存在或是为null。
### 外键基本语法
> foreign key(外键字段) references 主表(字段);
+ 说明：
1. 外键字段在本表定义的
2. 外键字段是指向另外一张表的某个字段.
### 案例说明
1. 先创建主表从表
```sql
  --- 班级表
  create table `my_class`(
    class_id int primary key,
    class_name varchar(64) not null unique,
    class_intro text not null
  )charset=utf8 engine=innodb;

  ---学生表（从表）
  create table `my_stu`(
    id int primary key,
    name varchar(64) not null,
    class_id int,
    foreign key (class_id) references my_class(class_id)
  )charset=utf8 engine=innodb;
```
2. 当有外键，使用的使用要注意以下注意事项
+ 当给从表添加记录时，要外键的值已经在主表中存在，否则不能添加成功
+ 如果我们外键没有设置not null，那么外键的值可以是null，而且还可以是多个
### 实践一把
```sql
  /*先添加一个班级，然后才能添加学生*/
  insert into my_class values(1,'php','是世界上最好的语言');
  
  insert into my_stu values(100,'张三',1);
  
  select * from `my_stu`;
  select * from `my_class`;
```
### 外键的使用细节
+ 外键指向表的字段，要求是primary key 或者是unique
+ 表的类型是innodb, 这样的表才支持外键
+ 外键字段的类型要和主键/unqiue字段的类型一致(长度可以不同)
+ 外键字段的值，必须在主键字段中出现过，或者为null [前提是外键可以为null]
+ 一旦建立主外键的关系，数据不能随意删除和修改了[参照完整性]
+ 不指定外键，同样可以有不同表的字段相关联的关系，只是这种关系的维护，要由程序员自己维护(php)，mysql数据库不会去检查了
## 13.6 约束的综合练习题-练习约束
> 现在有一个商店的数据库，记录客户及其购物情况，由下面三个表组成：商品goods（商品号good_id,商品名good_name,单价unitprice,商品类别category,供应商provider）  
> 客户customer（客户号customer_id,姓名name,住址address,电邮email性别sex,身份证card_Id);  
> 购买purchase（购买订单号order_id，客户号customer_id,商品号goods_id,购买数量nums);
+ 建表，在定义中要求声明 [进行合理设计]：
1. 每个表的主外键；
2. 客户的姓名不能为空值；
3. 电邮不能够重复;
4. 客户的性别[男|女]
5. 单价unitprice 在 1.0 - 9999.99 之间 check
```sql
  ---商品表goods
  create table `goods`(
    goods_id int unsigned primary key,
    godds_name varchar(64) not null default '',
    unitprice decimal(10,2) not null default 0.0,
    category smallint unsigned not null default 0.0,
    provider varchar(64) not null default ''
  )charset=utf8 engine=innodb;

  ---客户表
  create table `customer`(
    customer_id int unsigned primary key,
    name varchar(64) not null default '',
    address varchar(256) not null default '',
    email varchar(6) unique,
    sex enum('男','女') not null default '男',
    card_id char(18) not null unique
  )charset=utf8 engine=innodb;

  ---购买表
  create table `purchase`(
    order_id int unsigned primary key,
    customer_id int unsigned,
    goods_id int unsigned,
    nums int not null default 0,
    /*定义外键*/
    foreign key(customer_id) references customer(customer_id),
    foreign key(goods_id) references goods(goods_id)
  )charset=utf8 engine=innodb;
```
## 13.6 如何对约束进行管理
> 在实际开发中，对表的约束的管理是重要的，因此一定要掌握
### 案例
> 如果在建表时忘记建立必要的约束，则可以在建表后使用alter table 命令为表增加约束
```sql
  create table customer2 (
    customer_id char(8) not null default '',
    name varchar(20),
    address varchar(30) not null default '',
    email varchar(30) ,
    sex enum('男','女') not null);
  )charset=utf8 engine=myisam;
```
1. 客户号设为主键
```sql
  alter table `customer2` add primary key(customer_id);
```
2. 客户的姓名不能为空值
```sql
  alter table `customer2` modify name varchar(20) not null default '';
```
3. 电邮不能够重复;--增加身份证也不重复
```sql
  alter table `customer2` add unique(email);
```
4. 客户的住址默认是'重庆市沙坪坝区'
```sql
  alter table `customer2` modify address varchar(30) not null default '重庆市沙坪坝',
```
## 13.7 删除约束
> 当不再需要某个约束时，可以删除
1. 删除主键约束
```sql
  alter table `customer2` drop primary key;
```
2. 删除not null unique
```sql
  alter table `customer2` modify email varchar(6);
```
3. 删除外键约束
第一步先查询外键的名称
```sql
  show create table `my_stu`;
```
删除
```sql
  alter table `my_stu` drop foreign key 外键名称;
```
