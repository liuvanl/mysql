# 第九章 mysql函数
## 9.1 日期函数
### 日期函数一览表
| 函数名 | 说明 |
| ---- | ---- |
| current_date() | 当前日期 |
| current_time() | 当前时间 |
| current_timestamp | 当前时间戳 |
| date(datetime) | 返回datetime的日期部分 |
| date_add(date2,interval) | 在date2中加上日期或时间 |
| date_sub(date2,interval) | 在date2中减去一个时间 |
| datediff(date1,date2) | 两个日期差（结果是天） |
| timediff(date1,date2) | 两个时间差（多少时多少分多少秒） |
| now() | 当前时间 |
| year/month/date(datetime)from_unixtime | 年月日 |
### 基本使用
```sql  
  select current_date() from dual;   ## current_date() 2020-03-02  
  select current_time() from dual;   ## current_time() 15:15:15  
  select current_timestamp() from dual;     ## current_timestamp 2020-03-02 15:15:15  
  select now() from dual;            ## now() 输出当前时间
  select date('2020-02-20 20:20:20') from dual;  ## date() 2020-02-20
  select date_add('2020-02-20',interval 4 day) from dual;     ## 2020-02-24
  select date_sub('2020-02-20',interval 4 day) from dual;     ## 2020-02-16
  select datediff(now(),'1949-10-01')/365 from dual;      ## 70.4658  
  select timediff(now()+10,now()) from dual;          ## 00:00:10
  select * from `emp` where month(hiredate) = 2;
  
  
  
  #查询1982年3月入职的员工
    select * from `emp` where year(hiredate)=1982 and month(hiredate)=1;
```
### 综合案例
```sql
  create table `mes` (
        id int,
        content varchar(256),
        sendtime datetime
    )charset = utf8 engine = myisam;

    insert into mes values(1,'hello1',now());
    insert into mes values(2,'hello2',now());
    insert into mes values(3,'hello3',now());
    insert into mes values(4,'hello4',now());
    
    1.显示所有留言信息，发布日期只显示日期，不用显示时间
    select id,content,date(sendtime) from `mes`;
    2.请查询在10分钟内发布的帖子
    select * from `mes` where now() <= date_add(sendtime,interval 10 minute);
    3.求出2020-02-10和2011-11-11相差多少天
    select datediff('2020-02-10','2011-11-11') from dual;
    4.用sql语句求出活了多少天
    select datediff(now(),'1997-08-11') from dual;
    5. 假设你能活100岁，请问你还能活多少天？
    // 首先求出活到多少年：出生日期+100年
    // 还能活多少天 = 活到多少年-现在
    select datediff(date_add('1997-08-11',interval 100 year),now()) from dual;
```
+ unix_timestamp() 返回一个秒数（整数）从1970-1-1 0:0:0到现在的一个秒数
```sql
  select unix_timestamp() from dual;
```
+ from_unixtime 将一个时间戳转成你指定的一个格式
```sql
  select from_unixtime(1583147970,'%Y-%m-%d %H:%i:%s');
```
### 案例
#### 介绍
> 在php开发中，我们设计表时，保存一个日期有两种方式
1. 使用date/datetime/timestamp表保存时间
2. 使用int unsigned 来保存时间
#### 在mysql控制台实现
```sql
  ## 建表
  create table `book`(
    id int unsigned not null default 0,
    mess varchar(64) not null default '',
    save_time int unsigned not null default 0
  )
  
  ## 添加数据
  insert into `book` values(1,'hello1',unix_timestamp());
  ## 查询
  select from_unixtime(sava_time,'%Y-%m-%d %H:%i:%s') from `book`;  
```
#### 使用Php实现
```php
  header('content-type:text/html;charset=utf8');
    // 连接数据库
    $mysql_link = @mysql_connect('localhost','root','');
    if(!$mysql_link){
        echo '数据库连接失败！';
        exit;
    }

    mysql_select_db('phpstudy');

    // 添加方式1
    $sql = "insert into `book` values(2,'hello2',unix_timestamp())";
    $res = mysql_query($sql);

    // 添加方式2
    $time_now = time();
    $sql2 = "insert into `book` values(3,'hello3',$time_now)";
    mysql_query($sql2);

    // 查询方式1
    $sql3 = "select id,mes,from_unixtime(sava_time,'%Y-%m-%d %H:%i:%s') as sava_time from `book`";
    $res1 = mysql_query($sql3);
    while($row = mysql_fetch_assoc($res1)){
        var_dump($row);
        echo '时间是'.$row['sava_time'];
    }

    // 查询方式2
    $sql4 = "select id,mes,sava_time from `book`";
    $res2 = mysql_query($sql4);
    while($row=mysql_fetch_assoc($res2)){
        var_dump($row);
        // 对取回的时间戳，进行格式转换
        // 1.直接使用一个函数，设置时区
        date_default_timezone_set('PRC');
        echo  '<br>时间是'.date('Y-m-d H:i:s',$row['sava_time']);
    }
```
#### 练习
1. 如何查询在10秒/1.5天/2小时内发布的消息
```sql
  select id,content,sendtime from `mes` where now() <= date_add(sendtime,interval 136810 second);
```
2. 如何查询在10秒/1天/2小时前发布的消息
```sql
  select id,content,sendtime from `mes` where now() > date_add(sendtime,interval 93610 second);
```
## 9.2 字符串函数
### 字符串函数一览图
| 函数名 | 说明 |
| ---- | ---- |
| charset(str) | 返回字符串字符集 |
| concat(string2[...]) | 连接字串 |
| insert(string,substring) | 返回substring在string中出现的位置，没有返回0 |
| ucase(string2) | 转换成大写 |
| lcase(string2) | 转换成小写 |
| left(string2,length) | 从string2的左边起取length个字符 |
| length(string) | string长度 |
| replace(str,search_str,replace_str) | 在str中用replace_str替换search_str |
| strcmp(string1,string2) | 逐字符比较两字符串大小 |
| substring(str,position[length]) | 从str的position开始，取length个字符 |
| ltrim(string2) rtrim(string2) trim | 去除前端空格或后端空格 |
### 举例说明
1. charset(str)返回字符串对应的字符集
```sql
  select charset(ename) from emp;
```
2. concat(str1,str2)用于连接字符串
```sql
  select concat(ename,'工作是',job) as worker from emp;
```
3. ucase/lcase字母的大小写转换
```sql
  select ucase('abc') from dual;
```
4. left/right(str,len)表示从str的左右边，取出len个字符串
```sql
  select left(ename,1) from emp;
```
5. legth(str)统计str的长度
```sql
  select length('中华') from dual;
```
6. replace(str,str_find,str_repl)在str这个字符串中，找str_find替换成str_repl
```sql
  select ename,replace(job,'MANAGER','经理') from emp;
```
7. substring(str,position,len)表从str的position位置开始，取出len个字符，如果不写len，表示从position开始取出所有字符position是从1开始计算
```sql
  select substring(ename,2) from emp;
```
## 9.3 数学函数
### 数学函数一览图
| 函数名 | 说明 |
| ---- | ---- |
| abs(num) | 绝对值 |
| bin(decimal_number) | 十进制转二进制 |
| ceiling(num) | 向上取整 |
| conv(number2,from_base,to_base) | 进制转换 |
| floor(number2) | 向下取整 |
| format(number,decimal_places) | 保留小数位数，格式化一把xx,xxx形式显示 |
| hex(decimalNumber) | 转十六进制 |
| least(number,number2) | 求最小值 |
| mod(numerator,denominator) | 求余 |
| rand(seed) | rand(seed)其范围为0<=v<=1.0 |
### 举例说明
1. abs(num)
``sql
  select abs(-1) from dual;
```
2. ceiling(num)
```sql
  select ceiling(-1.01) from dual;
```
3. floor(num)
```sql
  select floor(-1.99) from dual;
```
4. format(num,num2)将num进行格式化处理，并保留到小数点num2位
```sql
  select format(12332.123456,3);
```
5. mod(num1,num2) 求出num1%num2
```sql
  select mod(10,3) from dual;
```
6. rand() 返回一个0.0<=v<=1.0的随机数
```sql
  select round(rand()*10) from dual;
```
7. round(num1,num2)
```sql
  select round(rand(),1) from dual;
```
## 9.4 流程控制函数
### 流程控制一览图
| 函数名 | 说明 |
| ---- | ---- |
| if(expr1,expr2,expr3) | 如果expr1为true,则返回expr2 否则返回expr3 |
| ifnull(expr1,expr2) | 如果expr1不为空null,则返回expr1，否则返回expr2 |
### 案例说明
1. 请查询emp表，显示ename和sal,和comm,如果comm为null，则显示0.0
```sql
  select ename,sal,if(comm,comm,0.0) from emp;
```
2. 当我们需要多个分支判断时，就可以使用case..when then else end
```sql
  ## 如果该雇员的sal在小于等于500，给工资涨20%
  ## 如果该雇员的sal在500<val<=1000，给工资涨10%
  ## 如果该雇员的sal在1000<val<=2000,给工资涨5%
  ## 其它情况不涨工资
  select ename,sal,
  (case
    when sal<=500 then sal*1.2
    when 500 < sal <= 1000 then sal*1.1
    when 1000 < sal <= 200 then sal * 1.05
    else sal
  end) as newSal from emp;
```
## 9.5 其它函数
### 其它函数一览图
| 函数名 | 说明 |
| ---- | ---- |
| user | 查询用户 |
| database | 数据库名称 |
| md5(str) | 为字符串算出一个md5 128比特检查和，通常用于对应用程序，使用到的表的某个字段（比如用户密码）加密 |
| password(str) | 从原文密码str计算并返回密码字符串，通常用于对mysql数据库的用户密码加密 |
### 举例说明
1. user()可以显示当前用户名
```sql
  select user() from dual;
```
2. database()可以显示你当前正在操作的哪个数据库
```sql
  select database() from dual;
```
3. md5(str)将你给的字符串，加密/编码成一个长度为32个字符的字符串，一般用于保存我们的密码
```sql
  insert into user500 values(1,md5('abc'));
```
4. password(str)可以将一个字符串，加密/编码成一个新的字符，比如我们的mysql user表，密码就是password加密
```sql
  select password('root') from dual;
```
