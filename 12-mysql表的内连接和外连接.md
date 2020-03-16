# 第十二章 mysql表的内连接和外连接
## 12.1 mysql表的内连接
### 基本介绍
> 内连接实际上就是利用where子句对两张（多表）表形成的笛卡尔集进行筛选，我们前面学习的查询都是内连接,也是在开发过程中用的最多的连接查询。
### 基本语法
> select 列名 from 表1,表2,表3 where 条件
> select 列名 from 表1,表2 inner join 表3 on 条件
### 举例说明
> 雇员的名字，薪水，部门名称
```sql
  ename,sal,dname
  select ename,sal,dname from emp,dept where emp.deptno = dept.deptno;
  select ename,sal,dname from emp inner join dept on emp.deptno=dept.deptno;
```
## 12.2 mysql的外连接
> mysql的外连接有两种，左外连接，右外连接。【完全外连接[mysql中支持语法，但是没有实际用处]】
### 左外链
#### 基本介绍
> 如果左侧的表完全显示我们就说是左外连接
#### 基本语法
> select 列名 from 表1 left join 表2 on 条件
#### 案例说明
1. 创建student学生表
```sql
  ## student表
  列名: id name
  create table `student`(
    id int not null,
    name varchar(64) not null default ''
  )charset = utf8 engine=myisam;
  
  ## 添加数据
  insert into `student` values(1,'Jack');
  insert into `student` values(2,'Tom');
  insert into `student` values(3,'Kity');
  insert into `student` values(4,'nono');
```
2. 创建exam考试成绩表
```sql
  create table `exam`(
    id int not null,
    grade float(4,1) not null default 0
  )charset=utf8 engine=myisam;
  
  insert into `exam` values(1,56);
  insert into `exam` values(2,76);
  insert into `exam` values(11,8);
```
3. 显示所有人的成绩，如果没有成绩，也要显示该人的姓名和id号,成绩显示为空
```sql
    select student.id,name,ifnull(grade,0) from student left join exam on student.id = exam.id;
```
### 右连接
### 基本介绍--->如果右侧的表完全显示我们就说是右外连接
### 基本语法
> select 列名 from 表1 right join 表2 on 条件
### 案例说明
> 查询结果要求：如果一个成绩，没有对应的人，则显示 '无名' , '未知id'
```sql
  select ifnull(name,'无名') as name,exam.id,grade from student right join exam on student.id = exam.id;
```
### 练习题
> 实现学生表(student)和考试成绩（exam）的完全显示效果?
```sql
  ## 1.先将学生的信息全部显示
  select student.id,name,grade from student left join exam on student.id = exam.id;
  ## 2. 将考试成绩都显示出来
  select student.id,name,grade from student right join exam on student.id = exam.id;
  ## 3.使用union合并查询
  select student.id,name,grade from student left join exam on student.id = exam.id union
  select exam.id,name,grade from student right join exam on student.id = exam.id;
```
### 练习2
> 列出雇员的名称，薪水，所在部门编号和名字，同时列出哪些没有员工的部门，要求两种方式实现：
1. 使用左外连接
```sql
  dname,sal,deptno,ename
  
  select dname,sal,dept.deptno,ename
  from dept left join emp on emp.deptno = dept.deptno;  
  
```
2. 使用右外连接
```sql
  select dname,sal,dept.deptno,ename from emp right join dept on emp.deptno = dept.deptno;
```
