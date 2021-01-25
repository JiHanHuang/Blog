---
title: 《mysql必知必会》读书笔记
date: 2020-11-05 16:11:48
categories: 笔记
tags: [mysql]
---

mysql初学者报道(•̀⌄•́)
　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　——　by JiHan
* * *
*以下内容都是根据书中的内容，以及自己觉得有价值的部分进行记录。可供mysql的信息快捷检索*

<!-- more -->

# 《mysql必知必会》读书笔记

## 了解mysql
首先mysql是一个关系型数据库，设计的基本原则就是将现实种的一类事物对应一个表，事物之间的关系反应到表之间的关系。
**SQL**是一种专门用来与数据库通信的语言
**表（table）** 某种`特定类型数据`的结构化清单。比如顾客的清单和订单就应当放在两个表中。
**模式（schema）** 关于数据库和表的布局及特性的信息。（理解不了，就不管）
**列（column）** 表中的一个字段。所有表都是由一个或多个列组
成的。
**数据类型（datatype）** 所容许的数据的类型。每个表列都有相应的数据类型，它限制（或容许）该列中存储的数据。
**行（row）** 表中的一个记录。
**主键（primary key）** 一列（或一组列），其值能够唯一区分表中每个行。
主键需要满足的条件：
* 任意两行都不具有相同的主键值；
* 每个行都必须具有一个主键值（主键列不允许NULL值）。

主键的最好习惯：
* 不更新主键列中的值；
* 不重用主键列的值；
* 不在主键列中使用可能会更改的值。
  
**外键（foreign key）**外键为某个表中的一列，它包含另一个表的主键值，定义了两个表之间的关系。
**关系表**的设计就是要保证把信息分解成多个表，一类数据一个表。各表通过某些常用的值（即关系设计中的关系（relational））互相关联。
**可伸缩性（scale**）能够适应不断增加的工作量而不失败。设计良好的数据库或应用程序称之为可伸缩性好（scale well）。

## MySQL简介
分为客户端(mysql)，和服务端(mysqld)。

##  使用MySQL
**先看本书的目录，进行环境安装和基础表的导入** [下载](./《mysql必知必会》读书笔记/mysql_scripts.zip)
linux，只用命令行。我的环境：CLOUD
**使用前准备**
服务端安装：
mysql版本5.7
```
$ yum install mysql？？？？感觉还缺点啥
$ systemctl start mysqld
```
安装完成后，会在日志里产生一个随机密码，通过这个随机密码，进入数据库。
下面的命令可以修改密码：
`mysql -u$db_user -p"$tmpPass" --connect-expired-password -e "ALTER USER '$db_user'@'localhost' IDENTIFIED BY '$new_pass';flush privileges;"`
mysql默认配置文件`/etc/my.cnf`
mysql默认日志输出`/var/log/mysqld.log`

当我们需要用客户端连接到服务端时，需要：
* 主机名（计算机名）——如果连接到本地MySQL服务器，为localhost；
* 端口（如果使用默认端口3306之外的端口）；
* 一个合法的用户名；
* 用户口令（如果需要）。

链接mysql：`mysql -uroot -pxxx -P 3306 -h localhost`（具体看`mysql --help`）

导入示例数据：
```
$ unzip mysql_scripts.zip
mysql> CREATE DATABASE test;
mysql> USE test;
mysql> source /path/create.sql
mysql> source ~/populate.sql
```

### mysql的sql命令
sql命令不区分大小写，但是表、数据库等名称是区分大小写的。
过个空格或者分行，等同于一个空格。
单个命令，我们称为一个子句，例如SELECT和FROM
#### 帮助:
`HELP cmd;`
`HELP SHOW;`
#### 数据库相关：
数据库创建：`CREATE DATABASE name;`
数据库选择：`USE name;`
数据库信息查看：`SHOW DATABASES;`
数据库删除：`DROP DATABASE dbname;`
#### 表相关：
##### 信息显示：
表列表：`SHOW tables;`
列显示：`SHOW columns FROM table_name;`

##### 普通检索：
检索单个列：`SELECT prod_name FROM table_name;`（没有明确排序的情况，顺序不能保证一致性。）
检索多个列：`SELECT prod_name1,  prod_name2 FROM table_name;`
检索所有列：`SELECT * FROM table_name \G;` (\G是更加友好是输出方式)
检索列值不同的行：`SELECT DISTINCT prod_name1 FROM table_name;` (不会显示prod_name1值相同的行)
检索行限制：`SELECT prod_name1 FROM table_name LIMIT 5;` (LIMIT 3,4 表示从3行开始的4行)
检索列和表限制：`SELECT products.prod_name FROM test.products;` (假设test数据库存在)

##### 数据排序：
mysql在排序时认为A和a是一样的。
根据某列排序：`SELECT prod_name FROM products ORDER BY prod_id;`
根据多行排序：`SELECT prod_id, prod_price, prod_name FROM products ORDER BY prod_price, prod_name;` （先按照价格排序，再按照名称排序）
排序方向：`SELECT prod_name FROM products ORDER BY prod_id DESC;`(默认情况下是升序，降序需要指定关键字`DESC`)
先降后升：`SELECT prod_id, prod_price, prod_name FROM products ORDER BY prod_price DESC, prod_name;` (升序关键字`ASC`,一般默认升序，所以没啥用)

##### 数据过滤：
**常用过滤**：
`SELECT prod_price, prod_name FROM products WHERE prod_price = 2.50;`(字符比较时，默认不区分大小写)
范围检查：`SELECT prod_price, prod_name FROM products WHERE prod_price BETWEEN 5 AND 10;`
空值检查：`SELECT cust_id FROM customers WHERE cust_email IS NULL;`
WHERE操作符：

| 操作符 | 说明|
|---|---|
|= |等于|
|<> |不等于|
|!=| 不等于|
|< |小于|
|<=| 小于等于|
|> |大于|
|>=| 大于等于|
|BETWEEN |在指定的两个值之间|
|IS NULL| 检查空值|

多个逻辑过滤组合，AND优先。最好是加上`()`确保逻辑正确。
逻辑过滤：`SELECT prod_price, prod_name FROM products WHERE vend_id = 1002 OR vend_id=1003;`
逻辑过滤：`SELECT prod_price, prod_name FROM products WHERE vend_id IN (1002,1003);`（效果同上的OR操作）
WHERE逻辑操作符：

| 操作符 | 说明|
|---|---|
|AND|同时满足|
|OR|满足其一|
|IN|满足其一|
|NOT|取反|


**通配符过滤**：
`SELECT prod_id, prod_name FROM products WHERE prod_name LIKE 'jet%'`（`%`类似正则里的`*`）

| 通配符 | 说明|
|---|---|
|%|0~n个任意字符|
|_|1个任意字符|

**正则匹配**：
`SELECT prod_id, prod_name FROM products WHERE prod_name REGEXP '1000';`

**字符类**

|类| 说明|
|---|---|
|[:alnum:]| 任意字母和数字（同[a-zA-Z0-9]）|
|[:alpha:]| 任意字符（同[a-zA-Z]）|
|[:blank:]| 空格和制表（同[\\t]）|
|[:cntrl:]| ASCII控制字符（ASCII 0到31和127）[:digit:] 任意数字（同[0-9]）|
|[:graph:] |与[:print:]相同，但不包括空格|
|[:lower:] |任意小写字母（同[a-z]）|
|[:print:]| 任意可打印字符|
|[:punct:] |既不在[:alnum:]又不在[:cntrl:]中的任意字符|
|[:space:]| 包括空格在内的任意空白字符（同[\\f\\n\\r\\t\\v]）|
|[:upper:]| 任意大写字母（同[A-Z]）|
|[:xdigit:] |任意十六进制数字（同[a-fA-F0-9]）|

**重复元字符元**

| 字|  符说  明|
|---|---|
|* |0个或多个匹配|
|+ |1个或多个匹配（等于{1,}）|
|? |0个或1个匹配（等于{0,1}）|
|{n}| 指定数目的匹配|
|{n,} |不少于指定数目的匹配|
|{n,m} |匹配数目的范围（m不超过255）|

**定位元字符元**

| 字|  符说  明|
|---|---|
|^ |文本的开始|
|$ |文本的结尾|
| \[[:<:]] |词的开始|
| \[[:>:]] |词的结尾|

##### 计算字段：
拼接字段：`SELECT concat(vend_name ,'(', vend_country,')') FROM vendors;`（concat关键字做拼接）
`RTrim()`去除右侧多余空格
`LTrim()`去除左侧多余空格
使用别名：`SELECT concat(vend_name ,'(', vend_country,')') AS new_name FROM vendors;`（AS字段赋予新的列名）
算术计算：`SELECT prod_id, quantity * item_price AS expanded_price FROM orderitems;` 算术操作符(+、-、*、/)


##### 函数：
函数可移植性不如SQL语句。
SQL支持的一些函数：
1. 文本处理函数
2. 数值数据运算
3. 日期处理
4. 返回DBMS正使用的特殊信息

**常用的文本处理函数**

| 函  数 |  符说  明|
|---|---|
|Left() |返回串左边的字符|
|Length()| 返回串的长度|
|Locate()| 找出串的一个子串|
|Lower() |将串转换为小写|
|LTrim() |去掉串左边的空格|
|Right() |返回串右边的字符|
|RTrim() |去掉串右边的空格|
|Soundex()| 返回串的SOUNDEX值，读音相近值|
|SubString() |返回子串的字符|
|Upper() |将串转换为大写|
示例：`SELECT cust_name, cust_contact FROM customers WHERE Soundex(cust_contact) = Soundex('Y Lie');`

**常用日期和时间处理函数**

| 函  数 |  符说  明|
|---|---|
|AddDate() |增加一个日期（天、周等）|
|AddTime() |增加一个时间（时、分等）|
|CurDate() |返回当前日期|
|CurTime() |返回当前时间|
|Date() |返回日期时间的日期部分|
|DateDiff() |计算两个日期之差|
|Date_Add()| 高度灵活的日期运算函数|
|Date_Format()| 返回一个格式化的日期或时间串|
|Day() |返回一个日期的天数部分|
|DayOfWeek() |对于一个日期，返回对应的星期几|
|Hour() |返回一个时间的小时部分|
|Minute() |返回一个时间的分钟部分|
|Month() |返回一个日期的月份部分|
|Now()| 返回当前日期和时间|
|Second()| 返回一个时间的秒部分|
|Time()| 返回一个日期时间的时间部分|
|Year() |返回一个日期的年份部分|
示例：`SELECT * FROM orders WHERE Date(order_date) BETWEEN '2005-09-01' AND '2005-09-30';`

**常用数值处理函数**

| 函  数 |  符说  明|
|---|---|
|Abs() |返回一个数的绝对值|
|Cos() |返回一个角度的余弦|
|Exp() |返回一个数的指数值|
|Mod() |返回除操作的余数|
|Pi() |返回圆周率|
|Rand() |返回一个随机数|
|Sin() |返回一个角度的正弦|
|Sqrt() |返回一个数的平方根|
|Tan() |返回一个角度的正切|

**SQL聚集函数**针对行组的操作函数

| 函  数 |  符说  明|
|---|---|
|AVG() |返回某列的平均值|
|COUNT() |返回某列的行数|
|MAX() |返回某列的最大值|
|MIN() |返回某列的最小值|
|SUM() |返回某列值之和|
示例：`SELECT AVG(prod_price) AS avg_price FROM products;`，计算改列平均值。
COUNT(*)返回满足条件的数据行数，包括NULL，COUNT(column)不包含NULL

##### 分组数据
创建分组：`SELECT vend_id, COUNT(*) AS num_prods FROM products GROUP BY vend_id;` 其中`GROUP BY`按照`vend_id`排序进行分组。
GROUP BY子句必须出现在WHERE子句之后，ORDER BY子句之前。
过滤分组：`SELECT vend_id, COUNT(*) AS num_prods FROM products GROUP BY vend_id HAVING COUNT(*) >= 3;`
`HAVING`和`WHERE`的差别: WHERE在数据分组前进行过滤，HAVING在数据分组后进行过滤.


##### SELECT子句顺序

|子  句|说  明|是否必须使用|
|---|---|---|
|SELECT| 要返回的列或表达式|是|
|FROM |从中检索数据的表|仅在从表选择数据时使用|
|WHERE |行级过滤|否|

##### 子查询
意味着把子查询语句查询的结果作为查询语句`where`的条件
示例：`SELECT cust_id FROM orders WHERE order_num IN (SELECT order_num FROM orderitems WHERE prod_id = 'TNT2');`
应当保证`where`子句中的列和子查询中`select`的列相同
也可以在计算字段使用子查询：`SELECT cust_name, cust_state, (SELECT COUNT(*) FROM orders WHERE orders.cust_id = customers.cust_id ) AS orders FROM customers;`
在进行子查询使用时，注意限制歧义性列名，比如这里用的`orders.cust_id = customers.cust_id `
*编写子查询的时候最好逐步进行，一层层嵌套验证*

##### 联结查询
其实就是多条件查询，不过把主键和外键相互关联而已。链接的表越多，性能下降越厉害，毕竟需要提前列出行乘积的量。
等值联结示例：`SELECT vend_name, prod_name, prod_price FROM vendors, products WHERE vendors.vend_id = products.vend_id ORDER BY vend_name , prod_name ;`（注意避免出现列名的二义性），如果没有where语句，那么列出的数据将是对应数据在所在表的行乘积(不懂就自己试试)。
内部联结示例(能达到上例一样的结果)：`SELECT vend_name, prod_name, prod_price FROM vendors INNER JOIN products ON vendors.vend_id = products.vend_id  ORDER BY vend_name , prod_name ;`
还有自连接、自然链接和外部链接。但是我感觉用where都能实现，就不搞这些了。

##### 组合查询
将多个select查询语句，组合成单个查询语句返回。(多个查询语句的返回结果相似)
示例：
`SELECT vend_id ,prod_id,prod_price FROM products WHERE prod_price <= 5;` + `SELECT vend_id ,prod_id,prod_price FROM products WHERE vend_id IN (1001,1002);`
\=
`SELECT vend_id ,prod_id,prod_price FROM products WHERE prod_price <= 5 UNION SELECT vend_id ,prod_id,prod_price FROM products WHERE vend_id IN (1001,1002);`
\=
`SELECT vend_id ,prod_id,prod_price FROM products WHERE prod_price <= 5 OR vend_id IN (101001,1002);`
`UNION`和`WHERE OR`都能去除重复行，但如果要包含重复行，只有`UNION ALL`可以。`UNION `只能在最后一个`SELECT`子句中包含`ORDER BY`子句

##### 全文本搜索
需要引擎支持一般MyISAM支持，InnoDB不支持
需要启动全文本搜索支持(FULLTEXT)，mysql会建立相关索引
建立全文搜索：`CREATE TABLE productfulltext ( note_id int NOT NULL AUTO_INCREMENT, prod_id char(10) NOT NULL, note_text text  NULL, PRIMARY KEY(note_id), FULLTEXT(note_text)) ENGINE=MyISAM;`
建议不要在导入数据时使用FULLTEXT，导入后修改表定义FULLTEXT会快点。
执行全文搜索：`SELECT note_text FROM productnotes WHERE Match(note_text) Against('rabbit');` Match()指定被搜索的列，Against()指定要使用的搜索表达式。
Match()参数必须和FULLTEXT中定义的相同，如果是多列，必须一致。
同样效果：`SELECT note_text FROM productnotes WHERE note_text LIKE '%rabbit%';`. 区别在于`LIKE`返回的数据顺序不确定，`Match`有良好的排序。
使用扩展查询：`SELECT note_text FROM productnotes WHERE Match(note_text) Against('rabbit' WITH QUERY EXPANSION);`
使用布尔文本搜索(在没有`FULLTEXT`下也能使用，但很缓慢)：``SELECT note_text FROM productnotes WHERE Match(note_text) Against('rabbit' IN BOOLEAN MODE);`
布尔搜索支持通配：

|布尔操作符|说  明|
|---|---|
|+| 包含，词必须存在|
|- |排除，词必须不出现|
|>| 包含，而且增加等级值|
|< |包含，且减少等级值|
|()| 把词组成子表达式（允许这些子表达式作为一个组被包含、排除、排列等）|
|~| 取消一个词的排序值|
|* |词尾的通配符|
|""| 定义一个短语（与单个词的列表不一样，它匹配整个短语以便包含或排除这个短语）|

一些特殊说明：
* 在索引全文本数据时，短词被忽略且从索引中排除。短词定义为那些具有3个或3个以下字符的词（如果需要，这个数目可以更改）。
* MySQL带有一个内建的非用词（stopword）列表，这些词在索引全文本数据时总是被忽略。如果需要，可以覆盖这个列表（请参阅MySQL文档以了解如何完成此工作）。
* 许多词出现的频率很高，搜索它们没有用处（返回太多的结果）。因此，MySQL规定了一条50%规则，如果一个词出现在50%以上的行中，则将它作为一个非用词忽略。50%规则不用于IN BOOLEAN MODE。
* 如果表中的行数少于3行，则全文本搜索不返回结果（因为每个词或者不出现，或者至少出现在50%的行中） 。
* 忽略词中的单引号。例如，don't索引为dont。
* 不具有词分隔符（包括日语和汉语）的语言不能恰当地返回全文本搜索结果。如前所述，仅在MyISAM数据库引擎中支持全文本搜索。


##### 插入数据
示例：`INSERT INTO customers VALUES(NULL, 'pep', '100 main', 'Los', 'CA', '90046', 'USA', NULL, NULL);`
插入数据必须对应着每一个列。
更安全的示例：`INSERT INTO customers(cust_name, cust_address, cust_city, cust_state, cust_zip, cust_country, cust_contact, cust_email) VALUES(NULL, 'pep', '100 main', 'Los', 'CA', '90046', 'USA', NULL, NULL);` 这种方式，后面的数据只需要和前面列名相对于就行(但是省略掉的列必须可为空或者有默认值)。
`INSERT LOW_PRIORITY INTO`方式插入可以降低插入的优先级，保证查询的效率。
插入多个列示例：`INSERT INTO customers(cust_name, cust_address, cust_city, cust_state, cust_zip, cust_country, cust_contact, cust_email) VALUES(NULL, 'pep', '100 main', 'Los', 'CA', '90046', 'USA', NULL, NULL), (NULL, 'pep', '100 main', 'Los', 'CA', '90046', 'USA', NULL, NULL)`
将数据从一张表导入到另外一张表：`INSERT INTO customers(cust_name, cust_address, cust_city, cust_state, cust_zip, cust_country, cust_contact, cust_email) SELECT cust_name, cust_address, cust_city, cust_state, cust_zip, cust_country, cust_contact, cust_email FROM custnew;`

##### 更新数据
示例：`UPDATE customers SET cust_email = 'elmer@fudd.com' WHERE cust_id = 10005;`
更新多列：`UPDATE customers SET cust_name = 'foo', cust_email = 'elmer@fudd.com' WHERE cust_id = 10005;` 更新多行默认情况下其中一个更新出错，所有更新数据都会恢复。如果需要发生错误也要继续更新，可使用关键字`IGNORE`
删除相应值，可以将其`UPDATE`为`NULL`（前提允许为`NULL`）
**如果忽略`WHERE`就会更新所有行**

##### 删除数据
删除行：`DELETE FROM customers WHERE cust_id = 10006;`
**同样，如果忽略`WHERE`就会删除所有行**
如果需要删除表中所有行，使用`TRUNCATE TABLE`更佳
最好使用强制实施引用完整性的数据库，这样MySQL将不允许删除具有与其他表相关联的数据的行。

##### 创建表
示例：
```sql
CREATE TABLE customers
(
  cust_id      int       NOT NULL AUTO_INCREMENT,
  cust_name    char(50)  NOT NULL ,
  cust_address char(50)  NULL ,
  cust_city    char(50)  NULL ,
  cust_state   char(5)   NULL ,
  cust_zip     char(10)  NULL ,
  cust_country char(50)  NULL ,
  cust_contact char(50)  NULL ,
  cust_email   char(255) NULL ,
  PRIMARY KEY (cust_id)
) ENGINE=InnoDB;
```
创建表时需要改表名不存在，不然会报错。
创建表的时候，`NULL`和空串不是一回事。`NULL`代表没有值，空串代表有值且值为`''`
创建主键：`PRIMARY KEY (vend_id, ...)`主键支持一个或多个，但行值必须唯一。主键不能配置为`NULL`值

#### 其他：
服务器状态信息：`SHOW status;`
显示用户安全权限：`SHOW grants;`
显示日志信息：`SHOW errors/warnings;`


mysql性能优化：
1. 尽量在数据库中进行过滤，而不是到客户端进行过滤。可有效减少带宽，并且数据库对过滤做了优化。


阅读位置：第18章