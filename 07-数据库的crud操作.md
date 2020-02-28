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
## 7.3 update语句
> 基本介绍：update语句是修改数据的
```sql
  update tbl_name 
          set col_name1 = expr1,col_name2 = expr2…
          where where_definition;
```
1. update可以一次性修改多个字段
2. update可以通过where字句来挑选哪些记录可以被修改
3. update修改字段是表达式
### 快速入门案例
> 要求：将所有员工薪水修改为500元，将姓名为“小妖怪”的员工薪水修改为3000元，将“老妖怪”薪水在原有基础上增加1000元
```sql
  1. 将所有员工薪水修改为500元
  update employee set salary = 500;
  2. 将姓名为“小妖怪”的员工薪水修改为3000元
  update employee set salary = 500 where name = '小妖怪';
  3. 将“老妖怪”薪水在原有基础上增加1000元
  update employee set salary = salary + 1000 where name = '老妖怪';
```
### update细节说明
+ update语法可以用新值更新原有表行中的各列
+ set字句指示要修改哪些列和要给予哪些值
+ where字句指定应更新哪些行,如果没有where字句，则更新所有的行
+ 如果需要修改多个字段，可以通过set字段1=值1,字段2=值2
## 7.4 delete 语句
```sql
  delete from tbl_name
          where where_definition
```
> 说明：delete语句可以删除我们希望删除的记录，通过where字句来选择你要删除的记录，如果你不写，整个表的记录都全部删除了
+ 一个小技巧-如何复制一张表
> employee 表 => employee2;
1. create table employee2 like employee
2. insert into employee2 select * from employee
+ delete 语句的使用细节
1. 如果不使用where字句，将删除表中所有数据
2. delete语句不能删除某一列值（可以使用update设为null或者''）
3. 使用delete语句仅删除记录，不删除表的本身。如果删除表，使用drop table 语句
4. 同insert和update一样，从一个表中删除记录将引起其它表的参照完整性问题，在修改数据库数据时，头脑中应该始终不要忘记这个潜在的问题
+ delete和truncate语句区别
1. delete和truncate都可以删除记录
```sql
  truncate table empoyee2;
  delete from 表名;
```
2. 从执行速度上看truncate > delete
3. delete 语句可以带where字句，truncate不能带where，即truncate指令只能删除整张表的记录
4. delete会返回删除的记录数，而truncate返回0
## 7.5 select语句
### 基本介绍
```sql
  select distinct * column1,column2…from tablename;
```
1. 如果我们希望过滤重复的数据，则加上distinct
2. * 号表示将所有的字段都检索出来，一般来说我们开发中不会使用select * 语句，这种语句会返回所有字段，效率太低
4. 如果我们只希望检索某几列，则写清楚字段名就可以
5. 我们使用原则是，需要什么字段，就取回什么字段
### 练习
```sql
  1. 查询表中所有学生的信息
  select * from student;
  2. 查询表中所有学生的姓名和对应的英语成绩
  select stu_name,english_grade from student;
  3. 过滤表中重复数据[distinct]
  select distinct * from student;
```
### select语句可以对列进行运算
#### 练习
+ 在所有学生分数上加10分特长分（即查询所有学生总分再加10分）
```sql
  select stu_name,chainese_grade+math_grade+english_grade+10 from student;
```
+ 使用别名表示学生分数
```sql
  select stu_name,chainese_grade+math_grade+english_grade as total from student;
```
### 关于字段别名的写法还有另外三种
+ select 字段名 as 别名 from 表名<b>推荐使用</b>
+ select 字段名 as '别名' from 表名
+ select 字段名 别名 from 表名
+ select 字段名 '别名' from 表名
### 练习
```sql
  1. 在姓赵的学生分数上增加60%(即查询总分再加60%)
  select stu_name,(chainese_grade+math_grade+english_grade)*1.6 from `student` where stu_name like '赵%';
  2. 统计姓刘的学生总分
  select stu_name,chainese_grade+math_grade+english_grade from `student` where stu_name like '刘%';
  3. 使用别名表示学生的数学分数
  select stu_name,math_grade as math from `student`;
```
### where子句中经常使用的运算符
#### 一览图
| 类型 | 运算符 | 说明 |
| ---- | ---- | ---- |
| 比较运算符 | > < <= >= = != | 大于、小于、小于等于、大于等于、等于、不等于 |
| 比较运算符 | between and | 显示在某一区间 |
| 比较运算符 | like not like | 模糊查询 |
| 比较运算符 | is null | 判断是否为空 |
| 逻辑运算符 | and or not | 多个条件同时成立、一个条件任一成立、不成立 |
#### 举例说明
+ 案例1
```sql
  select * from student where math_grade between 80 and 90;
```
+ 案例2--查询id是100,200,300的
```sql
  select * from student where id = 100 or id = 200 or id = 300;
```
+ like和not like
```sql
  select * from student where stu_name like '赵%';
  select * from student where stu_name not like '赵%';
```
+ 关于is null 的使用
```sql
  create table test (
    id int,
    name varchar(64)
  )charset=utf8 engine=myisam;
  insert into test values(1,'aaa');
  insert into test values(2,null);
  // 查询名字为null的应该是
  select * from test where name is null;
```
## 7.6 order by 子句
> order by子句是用于排序的
### 基本语法
```sql
  select column1,column2,…
        from table
        order by column asc|desc;
```
### 说明
1. order by一般来说是放在sql语句最后
2. order by 字段名 asc|desc，asc表示升序排序，desc表示降序排序，默认是升序排序
3. order by 也可以对多个字段进行排序
#### 案例
```sql
  1. 对数学成绩排序后输出升序
  select stu_name from `student` order by math_grade;
  2. 对总分排序后输出，然后再按从高到低的顺序输出
  select stu_name,chainese_grade+math_grade+english_grade as total from `student` order by total desc;
  3. 对不姓刘的学生总分成绩排序输出
  select stu_name,chainese_grade+math_grade+english_grade as total from `student` where stu_name not like '刘%' order by total;
```
