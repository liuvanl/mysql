# 第十章 mysql查询的加强
## 10.1 查看表的结构
+ desc 表名;
+ show create table 表名;
## 10.2 使用算术表达式加强
> 显示每个雇员的年工资
```sql
  select ename,(ifnull(sal,0.0)+ifnull(comm,0.0))*12 as yearSal from `emp`;
```
## 10.3 where子句的加强
+ 使用where子句
```sql
  ## 1. 显示工资高于3000的员工
  select ename,sal from emp where sal > 3000;
  ## 2. 查找1982.1.1后入职的员工
  select * from emp where hiredate > '1982-1-1';
  ## 3. 显示工资在2000到2500对的员工情况
  select ename,sal from emp where sal between 2000 and 2500;
  select ename,sal from emp where sal >= 2000 and sal < 2500;
```
+ 使用like操作符
```sql
  ## 如何显示首字符为s的员工姓名和工资
  select ename,sal from emp where ename like 's%';
  select ename,sal from emp where 's' = left(ename,1);
  
  ## 如何显示第三个字符为大写o的所有员工姓名和工资
  select ename,sal from emp where ename like '__o%';
```
+ 在where条件中使用in
```sql
  ## 如何显示empno为123，345，800...的雇员情况
  select * from emp where empno in(123,345,800);
  
  ## 如何显示没有上级的雇员情况
  select * from emp where mgr is null;
```
## 10.4 逻辑操作符的加强
```sql
  ## 查询工资高于500或是岗位为manager的雇员，同时还要满足他们的姓名首字母为大写的j
  select * from emp where (sal > 500 or job = 'MANAGER') and ename like 'j%';
```
## 10.5 使用order by子句
```sql
  ## 如何按照工资从低到高的顺序显示雇员信息
  select * from emp order by sal;
  ## 按照部门升序而雇员的工资降序排列
  select * from emp order by deptno,sal desc;
```
## 10.6 mysql的分页查询
> 我们的记录很多，但是我们实际查询时，不可能把所有的记录都返回，而且一页一页返回，这时我们就会使用分页查询（limit）
### 基本语法
> 分页查询有两个重要的参数$pageSize表示一页显示几条记录  
> 分页查询$pageNow表示显示第几页
```sql
  select 列名 from 表名 limt($pageNow-1)*$pageSize;
```
+ 说明
> mysql的LIMIT的($pageNow-1)*$pageSize是从0开始计算的，因此如果LIMIT 0,2表示从第一条记录开始取，取2条
### 案例说明
```sql
  ## 按雇员的id升序取出，每页显示3条记录
  select * from emp order by empno LIMIT 0,3;
  select * from emp order by empno LIMIT 3,3;
```
## 10.7 聚合函数的加强-max,min,avg,sum,count
### 案例说明
1. 如何显示所有员工中最高工资和最低工资
```sql
  select max(sal),min(sal) from emp;
```
2. 如何显示所有员工的平均工资和工资总和
```sql
  select sum(ifnull(sal,0.0)),avg(ifnull(sal,0.0)) from emp;
```
3. 计算共有多少员工
```sql
  select count(*) from emp;
```
4. 请统计部门编号为10的平均工资
```sql
  select deptno,round(avg(sal),2) from emp where deptno = 10;
```
### 案例练习
1. 请显示工资最高的员工名字，工作岗位 => 使用一个子查询
```sql
  ## 先查询最高工资
  select max(sal) from emp;
  ## 根据最高工资找到雇员信息
  select * from emp where sal = (select max(sal) from emp);
```
2. 请显示工资高于平均工资的员工信息
```sql
  ## 先查询平均工资
  select round(avg(sal),2) from emp;
  select * from emp where sal > (select round(avg(sal),2) from emp);
```
## 10.7 group by 子句和having子句的加强
### 案例练习
1. 如何显示每个部门的平均工资和最高工资
```sql
  select deptno,avg(sal),max(sal) from emp group by deptno;
```
2. 显示每个部门的每种岗位的平均工资和最低工资
```sql
  select deptno,job,avg(sal),min(sal) from emp group by deptno,job;
```
3. 显示平均工资低于2000的部门号和它的平均工资
```sql
  select deptno,avg(sal) as avgSal from emp group by deptno having avgSal < 2000;
```
### 案例练习2
+ 使用分组函数和数据分组子句
1. 显示所有雇员的平均工资、总工资、最高工资、最低工资
```sql
  select avg(sal),sum(sal),max(sal),min(sal) from emp;
```
2. 显示每种岗位的雇员总数、平均工资
```sql
  select job,count(*),avg(sal) from emp group by job;
```
3. 显示雇员总数，以及获得补助的雇员数
```sql
  select count(*),count(if(comm>0.0,1,null)) from emp;
```
4. 显示管理者的总人数
```sql
  select count(distinct mgr) from emp;
```
5. 显示雇员工资最大差额
```sql
  select max(sal) - min(sal) from emp;
```
6. 显示每个部门每个岗位的平均工资、每个部门的平均工资
```sql
  select deptno,job,avg(sal) from emp group by deptno,job;
  select deptno,avg(sal) from emp group by deptno;
```
## 10.8 关于分组关键字顺序问题
> 如果在select语句中同时包含有group by ,having,order by 那么他们的顺序是group by ,having,order by
### 案例
> 请统计各个部门的平均工资，并且是大于1000的，并且按照平均工资从高到低排序
```sql
  select deptno,avg(sal) as avgSal from emp group by deptno having avgSal > 1000 order by sal desc;
```
