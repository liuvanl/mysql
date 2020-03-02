# 第八章 select语句
## 8.1 基本介绍
```sql
  select distinct * column1,column2…from tablename;
```
1. 如果我们希望过滤重复的数据，则加上distinct
2. * 号表示将所有的字段都检索出来，一般来说我们开发中不会使用select * 语句，这种语句会返回所有字段，效率太低
4. 如果我们只希望检索某几列，则写清楚字段名就可以
5. 我们使用原则是，需要什么字段，就取回什么字段
## 8.2 练习
```sql
  1. 查询表中所有学生的信息
  select * from student;
  2. 查询表中所有学生的姓名和对应的英语成绩
  select stu_name,english_grade from student;
  3. 过滤表中重复数据[distinct]
  select distinct * from student;
```
## 8.3 select语句可以对列进行运算
### 练习
+ 在所有学生分数上加10分特长分（即查询所有学生总分再加10分）
```sql
  select stu_name,chainese_grade+math_grade+english_grade+10 from student;
```
+ 使用别名表示学生分数
```sql
  select stu_name,chainese_grade+math_grade+english_grade as total from student;
```
## 8.4 关于字段别名的写法还有另外三种
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
## 8.5 where子句中经常使用的运算符
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
## 8.6 order by 子句
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
## 8.7 合计/聚合函数-count
### 基本介绍
> count函数是进行统计满足条件的记录数有多少条，是按照分组的形式统计
### 基本语法
```sql
  select count(*)|count(列名) from table_name
            where where_definition
```
### 举例说明
```sql
    1. 统计一个班级共有多少学生
    select count(stu_name) from student;
    2. 统计数学成绩大于90的学生有多少个
    select count(math_grade) from student where math_grade > 90;
    3. 统计总分大于250的人数有多少？
    select count(*) as nums from student where chainese_grade + math_grade+english_grade > 250;
```
### count细节
1. count(*) 和count(列名)的区别
+ count(*) 不会忽略null
+ count(列名)会忽略null值
## 8.8 聚合函数-sum
### 基本介绍
> sum函数会返回满足where条件的和
### 基本语法
```sql
  select sum(列名) from tablname
    where where_definition
```
### 案例说明
```sql
  1. 统计一个班级数学总成绩
  select sum(math_grade) from student;
  2. 统计一个班级语文、英语、数学各科的总成绩
  select sum(chainese_grade),sum(math_grade),sum(english_grade) from student;
  3. 统计一个班级语文、英语、数学的成绩总和
  ## 第一种方法
  select sum(chainese_grade) + sum(math_grade) + sum(english_grade) from student;
  ## 第二种方法
  select sum(ifnull(chainese_grade,0.0) + ifnull(math_grade,0.0) + ifnull(english_grade,0.0)) from student;
  4. 统计一个班级语文成绩平均分
  select round(sum(chainese_grade) / count(*),2) as avg from student;
```
