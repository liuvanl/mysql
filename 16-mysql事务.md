# 第十六章 mysql事务
## 16.1 基本介绍
> 事务用于保证数据的一致性,它由一组相关的dml语句组成,该组的dml语句要么全部成功，要么全部失败。如:网上转账就是典型的要用事务来处理，用以保证数据的一致性。
## 16.2 mysql事务的关键词
+ start transaction 开始一个事务
+ savepoint 保存点
+ rollback to 回滚到某个保存点
+ rollback 回滚
+ commit 提交
## 16.3 事务的体验案例
1. 开始一个事务
```sql
  start transaction;
```
2. 做一个保存点
```sql
  savepoint a;
```
3. 删除用户
```sql
  delete from account where id = 100;
```
4. 做一个b保存点
```sql
  savepoint b;
```
5. 执行update
```sql
  update account set money = 6000 where id = 200;
```
6. 做一个c保存点
```sql
  savepoint c;
```
7. 可以回退到某个保存点
```sql
  rollback to b;
```
8. 当你觉得没有问题了就正式提交
```sql
  commit;
```
## 16.4 事务的细节
1. 没有设置保存点
> 开始事务时，事务会默认给你创建一个保存点，如果你希望回退也可以使用rollback , 就可以直接回退到事务开始的状态.
2. 多个保存点
> 我们可以设置多个保底点，但是如果我们回退时，需要按顺序回退。即如果你回退到前面的某个保存点，那么后面的保存点就没有了.
3. 存储引擎
> 如果要支持事务，需要存储引擎Innodb
4. 开启事务方式
> start transaction;
## 16.5 在Php中实现事务
```php
  <?php
    header('content-type:text/html;charset=utf8');	
    // 链接数据库
    $sql_link = @mysql_connect('localhost','root','');
    if(!$sql_link){
      echo '数据库链接失败！';
      exit;
    }
    // 选择数据库
    mysql_select_db('phpstudy');
    // 设置编码
    mysql_query('set names utf8');
    // 准备sql语句
    $sql1 = "update `test1` set money = money-100 where id = 1";
    $sql2 = "update `test1` set money = money+100 where id = 2";

    // 开启事务
    mysql_query('star transaction');

    // 加入事务控制
    $res1 = mysql_query($sql1);
    $res2 = mysql_query($sql2);

    if(!$res1 || !$res2){
      // 说明语句执行失败
      echo '<br>转账失败，执行回滚！';
      mysql_query('rollback');
    }else {
      echo '<br>转账成功';
      mysql_query('commit');
    }		
  ?>
```
## 16.6 事务的隔离级别(了解概念，实际开发中，使用不同)
### 基本概念
> 当不同的客户端或是线程，去访问mysql数据的同一张表时，mysql提供一种机制，来控制不同的客户端和线程对数据访问的权限. 隔离级别定义了事务与事务之间的隔离程度, 在不同的事务中，看到数据是不一样的.
### 事务隔离级别的分类(隔离程度是 从上到下，越来越强.)
1. 读未提交(read uncommitted)
2. 读已提交(rad committed)
3. 可重复读 (repeatable read)
4. 可串行化(serializable)
### 16.10	事务隔离级别带来的问题
1. 脏读
> 当一个事务读取另一个事务尚未提交的修改时，产生脏读
2. 不可重复读
> 同一查询在同一事务中多次进行，由于其他提交事务所做的修改或删除，每次返回不同的结果集，此时发生非重复读
3. 幻读（虚度）
> 同一查询在同一事务中多次进行，由于其他提交事务所做的插入操作，每次返回不同的结果集，此时发生幻读
