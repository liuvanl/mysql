# 第十九章 mysql用户的管理
## 19.1 mysql用户的基本介绍
> mysql用户都保存在mysql数据库的user表中，具体如下:
```sql
  select * from user;
```
+ 说明
1. Host表示该用户可以登录的ip, 如果是localhost表示只能本机登录，不能远程登录,注意在实际开发中，我们只能让用户本机登录mysql
2. User字段；用户名 ，说明在mysql中，一个完整的用户名是有User和Host组成比如 'root'@'localhost';
3. Password:用户密码, 使用password函数加密的.
## 19.2 mysql用户创建
+ 基本语法/指令
> create user '用户名'@'主机名' identified by '密码';
1. 这是创建一个用户是   '用户名'@'主机名'
2. identified by 后面可以指定密码
## 19.3 mysql用户删除
+ 基本语法
> drop user '用户名'@'主机名';
+ 修改自己的密码
```sql
  set password = password('密码');
```
+ 修改其它用户的密码
```sql
  set password for '用户名'@'主机名'=password('密码');
```
## 19.4 mysql用户赋权限
> 当我们新创建一个用户时，该用户除了可以登录，没有其它的权限
+ 基本语法
> grant 权限列表 ON 数据库.表（数据对象） TO '用户名'@'主机名'  [identified by '密码'];    数据对象包括 表  视图  函数 存储过程  触发器
1. 权限列表可以是 select , update , insert ,delete ...., 如果你希望将所有的权限都给某个用户，则可以写 all， 比如:
```sql
  grant select on phpstudy.emp to 'liuvanl'@'localhost';
```
+ 也可以一次性给多个权限
```sql
  grant select , delete , update to 数据库.表'hsp'@'localhost' ;
```
2. 数据库.表
+ 可以将某个库的某个表给某个用户使用,比如 itbull.emp 
+ 如果希望将某个库的所有的表都给用户，则可以 数据库.*, 比如itbull.*
+ 如果你希望将所有库的所有表都给用户，可以写成 *.*
3. 对identified by '密码' 的说明
+ identified by 可以省略，也可以写出
+ 如果用户存在，就是修改用户的密码
+ 如果该用户不存在，就是创建用户，并赋给权限
## 19.5 回收mysql用户权限
+ 基本语法
> revoke 权限列表 on 数据库.表名 from '用户名'@'主机名';  
> revoke 的权限列表和 数据库.表名的用法和前面 grant 是一样
+ 举例说明
```sql
  revoke select on itbull.emp from 'hsp'@'localhost';
```
## 19.6 查询mysql用户的权限
+ 基本语法
> show grants for '用户名'@'主机名';
## 19.7 让权限生效
> 如果我们给某个用户赋权限后，发现没有生效，则可以使用下面的指令，让权限立即管用.
+ 基本语法
```sql
  flush privileges;
```
## 19.8 用户管理的注意事项
1. 在创建用户的时候，如果不指定Host, 则为% , %表示表示所有IP都有连接权限
> 说明，这样创建用户很危险，该用户可以远程登录，所有不要这样使用.
2. 还可以这样创建用户
> create user  'xxx'@'192.168.0.%'  表示 xxx用户在 192.168.0.*的ip可以登录mysql
3. 在删除用户的时候，如果 host 不是 %, 需要明确指定  '用户'@'host'， 如果host是 %, 则删除用户不需要指定host
