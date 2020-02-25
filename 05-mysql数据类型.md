# 第五章 mysql数据类型
## 5.1 mysql数据类相关表格
### 数值类型一览表
|  数据类型  |  说明  |
|  ----  |  ----  |
|  <b>BIT(M)</b>  |  位类型，M指定位数，默认值1，范围1-64  |
|  <b>TINYINT</b>  |  带符号的范围是-128到127，无符号0到255，默认是有符号  |
|  BOOL,BOOLEAN  |  使用0或1表示真或假  |
|  SMALLINT  |  带符号是负的2的15次方到2的15次方-1，无符号2的16次方1  |
|  <b>INT</b>  |  带符号的范围是负的2的31次方到2的31次方-1，无符号2的32次方-1  |
|  BIGINT  |  带符号的范围是负的2的63次方到2的63次方-1，无符号2的64次方-1  |
|  <b>FLOAT(M,D)</b>  |  M指定显示长度,D指定小数位数，占用空间4个字符  |
|  DOUBLE(M,D)  |  表示比float精度更大的小数点，占用空间8个字符  |
|  <b>DECIMAL(M,D)</b>  |  M指定显示长度,D指定小数位数  |
### 文本、二进制类型一览表
|  数据类型  |  说明  |
|  ----  |  ----  |
|  <b>CHAR(SIZE)</b>  |  固定长度字符串 最大255  |
|  <b>VARCHAR(SIZE)</b>  |  可变长度字符串最大65532  |
|  BLOB LONGBLOB  |  二进制数据  |
|  <b>TEXT LONGBLOB</b>  |  大文本，不支持全文索引，不支持默认值，建议使用varchar  |
### 时间日期一览表
|  数据类型  |  说明  |
|  ----  |  ----  |
|  <b>DATE/DATETIME/TimeStamp</b>  |  日期类型（YYYY-MM-DD）（YYYY-MM-DD HH:MM:SS）,TimeStamp表示时间戳，它可用于自助记录insert、update操作的时间  |
### string类型
|  数据类型  |  说明  |
|  ----  |  ----  |
|  <b>ENUM类型（枚举）</b>  |  ENUM是一个字符串对象，其值来自表创建时在规定中显示枚举的一列值，在某些情况下，enum值也可以为空字符串("")或null  |
|  <b>SET类型（集合）</b>  |  set是一个字符串对象，可以有零或多个值，其值来自表创建时规定的允许的一列值，指定包括多个SET成员的SET列值时各成员之间用逗号('')间隔开  |
## 5.2 mysql数值类型-整型
> 基本介绍：用于保存整数的数据类型，常见有（tinyint smallint mediumint int bigint）
### 一览表
|  类型   | 字节  |  最小值  |  最大值  |
|  :----:  | :----:  |  :----:  | :----:  |
| <b>TINYINT</b>带符号  | 1 | -128 | 127 |
| <b>TINYINT</b>无符号  | 1 | 0 | 255 |
| SMALLINT带符号  | 2 | -32768 | 32767 |
| SMALLINT无符号  | 2 | 0 | 65535 |
| MEDIUMINT带符号  | 3 | -8388608 | 8388607 |
| MEDIUMINT无符号  | 3 | 0 | 16777215 |
| INT带符号  | 4 | -2147483648 | 2147483647 |
| INT无符号  | 4 | 0 | 4294967295 |
| BIGINT带符号  | 8 | -9223372036854775808 | 9223372036854775807 |
| BIGINT无符号  | 8 | 0 | 18446744073709551615 |
+ 说明
1. 从表访问来看 tinyint < smallint < mediumint < int < bigint
2. 数据类型占用的大小不一样 tinyint < smallint < mediumint < int < bigint
3. 整型分为两种，一种是有符号的，一种是无符号的
4. 不同的整型有范围的约束
+ 细节说明
1. 给某个整型设置有符号或无符号怎么做？
> 字段名 整型类型 unsigned -> 这个就是无符号的<br>
> 字段名 整型类型 -> 这个就是有符号
+ 关于zerofill的说明  zerofill叫做0填充
1. 当int(4)zerofill使用，如果添加的整数不够4位，则数值的左边使用0进行填充
2. int(4)不能理解为最大只能是4位数，而应该理解成是0填充的宽度
3. 当一个字段被zerofill修饰时，那么这个字段就自动成为unsigned
## 5.3 mysql数值类型-bit
> bit类型就是位类型
+ 基本使用案例
```sql
  create table test3 (
    id int,
    a bit(1)
  );
```
+ 细节问题
1. bit自动在显示时，按ascii码对应的字符显示
2. 查询时候仍然可以用数值
3. 位类型，m指定位数，默认值1，范围1.64  bit(1-64)，可以通过bit(M)-M值来控制我们填充数据的大小
4. bit类型，只能是unsigned
## 5.4 mysql数值类型-小数
> 通过bit(M)小数类型，在mysql中使用最多的就是float、decimal
+ 基本使用
1. float(M,D) M指定显示的长度，d指定小数位数，占用空间4个字节
> float(4,2)表示的范围是-99.99-99.99  
> float(4,2) unsigned表示的范围是0-99.99
2. decimal(M,D) M指定长度,D表示小数点的位数
> decimal(5,2)表示的范围是-999.99-999.99  
> decimal(5,2) unsigned表示的范围是0-999.99
## 5.5 mysql数据类型-字符串
> 基本介绍：mysql的字符串类型，用于存放字符串，最主要的有三种，分别是char,varchar,text
+ 基本使用
```sql
  create table `user300` (
    id int unsigned not null default 0,
    name varchar(64) not null default '',
    post_code char(6) not null default ''
  )charset = utf8 engine = myisam;
  
  insert into `user300` values(100,'liuvanl',400567);
```
+ 字符串使用的注意事项
1. char(n),varchar(n)那么这个n值到底有多大？char(n)这里的范围是1-255，最大255
> varchar(n)这里的n范围是和表的字符集有关系<br>
> 如果表的字符集为utf8，那么n的最大值是（65535-3）/3 = 21844<br>
> 如果表的字符集为gbk，那么n的最大值是（65535-3）/ 2 = 32766<br>
> 如果表的字符集为latin1 那么n的最大值是（65535-3）/1  = 65532<br>
+ 解释
> varchar最大是有65535个字节，但是需要预留3个字节<br>
> varchar的数据大小,有一个字节标识是否允许为null<br>
2. char(n)是定长，varchar(n)是变长
> char(4)是定长，就是说，即使你插入'aa'，也会占用分配的4个字符，varchar(4000)是变长，就是说，如果插入了'aa',实际占用空间大小是L+1
+ 举例说明
> char(4) => 添加 'aa' => 实际暂用的空间，就是4个字符占用的空间  
> varchar(4) => 添加 'aa' => 实际暂用的空间，就是2个字符占用的空间+1  
> 小结：当我们的某个字段内容长度不确定，我们建议使用varchar  
3. char(n)会将存入的最后空格自动删除，而varchar(n)会保留空格，所以如果你有这样的需求，就是在字段内容的最后保留空格，则应该选用varchar
4. text数据类型可以视为varchar，但是不用指定大小，他可以存放varchar最大的范围
5. 一个表的所有自定义的字段，他们定义长度加起来不能超过65535
6. 如果我们字段的长度要大于65535字节大小我们可以使用text来替代varchar
## 5.6 mysql数据类型-日期和时间
> 基本介绍：在mysql中，日期和时间类型主要有（1.date 2.datetime 3.timestamp）
+ 基本使用
```sql
  create table `user908`(
    id int,
    birthday date,
    card_time datetime,
    login_time timestamp
  )charset=utf8 engine=myisam;
  
  insert into `user908` values(100,'2020-02-25','2020-02-25 11:11:11','2020-02-25 12:12:12');
```
## 5.7 mysql数据类型-枚举enum，集合set
+ 看一个实际需求
> 有一个调查表votes，需求调查人的喜好，1比如（苹果，西瓜，菠萝）中去选择（可以多选）  2(男，女，保密)单选 3名字非空
+ 基本语法
> 如果多于多选我们可以使用set数据类型  
> 如果对于单选我们可以使用enum数据类型
```sql
  create table `votes` (
    id int not null default 1 comment 'id号',
    hobby set('苹果','西瓜','菠萝') not null default '苹果' comment '爱好',
    sex enum('男','女','保密') not null default '保密' comment '性别'
  )charset = utf8 engine=myisam;
  
  insert into `votes` values(100,'苹果','男');
  insert into `votes` values(200,'苹果,西瓜','男');
```
+ 细节说明
1. 在enum选择中，选项可以用数字表示：则添加时可以用数字表示
```sql
  insert into `votes` values(300,'西瓜','2');   // 此处2代表选中的是女
```
2. 在set选择中，也可以使用数字来表示选项
```sql 
  insert into `votes` values(400,2,2);
```
3. 如果查询set中的值 比如，查询喜欢吃苹果的人
```sql
  select find_in_set('苹果',hobby) from votes;
```
## 5.8 图片 电影 音频数据类型怎么存放
> 对于图片，电影，音频这样的数据，我们通常不会直接存在数据库中，而是记录该文件的路径，然后通过路径去读取该文件
```sql
  create table `user` (
    id int,
    name varchar(64),
    head_img varchar(128) // 记录这个头像的路径
  )
```
