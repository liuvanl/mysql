# 第二十六章mysql的基础
## 26.1数据库的介绍
> mysql数据库最重要的作用就是保存数据，mysql数据库利于数据的管理（增加，修改，删除，查询）
## 26.2数据库的分类
1. sql server数据库
2. oracle数据库
3. mysql数据库
4. DB2
5. informix
## 26.3使用命令行窗口链接mysql数据库
### 基本语法
> mysql -u 用户名 -p密码 -h主机名 -P端口
## 26.4关于mysql的其它相关的指令
1. 服务方式启动
> 计算机->管理->mysql服务->单机右键[选择启动，停止]
2. 通过命令行来进行管理
> <b>net start</b> mysql<br>
> <b>net stop</b> mysql
3. 如何在任何目录都可以连接到mysql
> 说明：当用户刚刚安装好mysql时，只能在mysql安装的目录/bin去操作mysql，其它目录你使用不了<br>
> 可以通过配置环境变量来搞定：计算机->属性->高级环境配置->环境变量->path
## 26.4sql语句的分类
1. ddl语句
> 数据库定义语句，常用的ddl语句有（create,drop,alter）
2. dml语句
> 数据库操作语句，常用的dml语句有（insert,update,delete）
3. dql语句
> 数据库查询语句，常用的有(select)
4. dcl语句
> 数据控制语句，用于管理数据库和用户的，常用的有(commit,savepoint,rollback,grant,revoke)
