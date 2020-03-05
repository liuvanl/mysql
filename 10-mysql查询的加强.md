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
