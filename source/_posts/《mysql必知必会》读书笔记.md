---
title: 《mysql必知必会》读书笔记
date: 2020-11-05 16:11:48
categories: 笔记
tags: [mysql]
---

mysql初学者报道(•̀⌄•́)
　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　——　by JiHan
* * *
*以下内容都是根据书中的内容，以及自己觉得有价值的部分进行记录。可供mysql的信息快捷检索。*
*本书是比较老的书了，基于的是MySQL5上进行编写的，与目前很多流行版本可能有所不符。但对于初学小白来说还是很有用的。*

<!-- more -->

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

**建表引擎**
* InnoDB是一个可靠的事务处理引擎，它不支持全文本搜索；
* MEMORY在功能等同于MyISAM，但由于数据存储在内存（不是磁盘）中，速度很快（特别适合于临时表）
* MyISAM是一个性能极高的引擎，它支持全文本搜索，但不支持事务处理。

##### 更新表
增加列：`ALTER TABLE vendors ADD vend_phone CHAR(20);`
删除列：`ALTER TABLE vendors DROP COLUMN vend_phone;`
定义外键：`ALTER TABLE orderitems ADD CONSTRAINT fk_orderitems_products FOREIGN KEY (prod_id) REFERENCES products (prod_id);`
使用`ALTER`命令一定要小心谨慎，提前做数据备份，因为数据库表更改是不能撤销的。

##### 删除表
示例：`DROP TABLE customers;` 不能撤销，没有确认，一次性永久删除。

##### 重命名表
示例：`RENAME TABLE customers2 TO customers;`
重命名多个：`RENAME TABLE customers2 TO customers, A TO A2;`

#### 视图
视图不包含任何数据，仅仅是一张虚拟表。
如：
我们需要查询一组数据：
```sql
SELECT cust_name , cust_contact 
  FROM customers, orders, orderitems 
  WHERE customers.cust_id = orders.cust_id 
    AND orderitems.order_num = orders.order_num 
    AND prod_id = 'TNT2';
```
而我们需要根据不同的`prod_id`得到不同的结果。为了简便，我们将前面相同的查询包装成一个表`productcustomers`虚拟表。得到以下查询：
`SELECT cust_name, cust_contact FROM productcustomers  WHERE prod_id = 'TNT2';`
而这里的`productcustomers`就是视图。
视图好处：
* 重用SQL语句。
* 简化复杂的SQL操作。在编写查询后，可以方便地重用它而不必知道它的基本查询细节。
* 使用表的组成部分而不是整个表。
* 保护数据。可以给用户授予表的特定部分的访问权限而不是整个表的访问权限。
* 更改数据格式和表示。视图可返回与底层表的表示和格式不同的数据。

视图常见的规则和限制：
* 与表一样，视图必须唯一命名（不能给视图取与别的视图或表相同的名字）。
* 对于可以创建的视图数目没有限制。
* 为了创建视图，必须具有足够的访问权限。这些限制通常由数据库管理人员授予。
* 视图可以嵌套，即可以利用从其他视图中检索数据的查询来构造一个视图。
* ORDER BY可以用在视图中，但如果从该视图检索数据SELECT中也含有ORDER BY，那么该视图中的ORDER BY将被覆盖。
* 视图不能索引，也不能有关联的触发器或默认值。
* 视图可以和表一起使用。例如，编写一条联结表和视图的SELECT语句。

使用视图并不能提升性能，只是做了一次包装，实际查询还是一样的过程。

##### 视图使用
* 视图用`CREATE VIEW`语句来创建。示例: 
  ```sql
  CREATE VIEW productcustomers AS
  SELECT cust_name , cust_contact 
    FROM customers, orders, orderitems 
    WHERE customers.cust_id = orders.cust_id 
      AND orderitems.order_num = orders.order_num
  ```
* 使用`SHOW CREATE VIEW viewname;`来查看创建视图的语句。
* 用DROP删除视图，其语法为`DROP VIEW viewname;`。
* 更新视图时，可以先用DROP再用CREATE，也可以直接用`CREATE OR REPLACE VIEW`。如果要更新的视图不存在，则第2条更新语句会创建一个视图；如果要更新的视图存在，则第2条更新语句会替换原有视图。

视图中还可以进行数据格式化、过滤和计算等。其使用方式和`SELECT`本质上一样

#### 存储过程
一个业务稍微复杂的业务，通常都需要很多个SQL语句配合才能完成。而这个时候，使用存储过程就可以将这些SQL语句整合到一起。可将其视为批处理，虽然不仅限于批处理。
使用存储过程好处(简单、安全、高性能)：
* 通过把处理封装在容易使用的单元中，简化复杂的操作（正如前面例子所述）。
* 由于不要求反复建立一系列处理步骤，这保证了数据的完整性和统一性
* 简化对变动的管理。如果表名、列名或业务逻辑（或别的内容）有变化，只需要更改存储过程的代码。使用它的人员甚至不需要知道这些变化。
* 提高性能。因为使用存储过程比使用单独的SQL语句要快。
* 存在一些只能用在单个请求中的MySQL元素和特性，存储过程可以使用它们来编写功能更强更灵活的代码。

##### 创建存储过程
创建存储过程（命令行下 有语法错误）：
```sql
CREATE PROCEDURE productpricing() 
  BEGIN  
    SELECT Avg(prod_price) AS priceaverage 
      FROM products; 
  END;
```
如果在命令行下执行，需要使用`DELIMITER`修改分隔符：
```sql
DELIMITER $

CREATE PROCEDURE productpricing() 
  BEGIN  
    SELECT Avg(prod_price) AS priceaverage 
      FROM products; 
  END$

DELIMITER ;
```
##### 调用存储过程
存储过程的执行为调用，调用方式和函数调用类似。
`CALL productprice(@pricelow, @pricehigh);`
存储过程定义的时候，参数可定义类型为`IN`、`OUT`和`INOUT`。用于表示参数是传入、传出还是即传入又传出。参数传递和定义类型一致。
例如：
```sql
CREATE PROCEDURE productpricing(
  OUT o DECIMAL(8,2),
  IN i DECIMAL(8,2),
  INOUT io DECIMAL(8,2)
) 
  BEGIN  
    SELECT Avg(prod_price) INTO o 
      FROM products; 
    -- Do some other things
  END;
```
如果我们想显示变量返回的值：`SELECT @pricelow， @pricehigh`方式。

##### 删除存储过程：
`DROP PROCEDURE productpricing;`
`DROP PROCEDURE IF EXISTS productpricing;`

##### 检查存储过程：
可以看到创建时间、创建者等一些详细信息。
`SHOW CREATE PROCEDURE productpricing`

##### 稍微复杂的存储过程代码
```sql
-- Name: ordertotar
-- Parameters: onumber = order number
--                     taxable = 0 if not taxable, 1 if taxable 
--                     ototal = order total variable

CREATE PROCEDURE ordertotal (
  IN onumber INT,
  IN taxable BOOLEAN, 
  OUT ototal DECIMAL (8,2)
) COMMENT 'Obtain order total, optionally adding tax'
BECIN

  -- Declare variable for total 
  DECLARE total DECIMAL (8,2); 
  -- Declare tax percentage 
  DECLARE taxrate INT DEFAULT 6;

  -- Get the order total.
  SELECT Sum(item_price*quantity) 
  FROM orderitems
  WHERE order_num = onumber 
  INTO total;

  -- Is this taxable? 
  IF taxable THEN
    -- Yes. so add taxrate to the total.
    SELECT total+(total/100*taxrate) INTO total; 
  END IF:

  ---And finally, save to out variable 
  SELECT total INTO ototal;

END;
```

##### 游标：
只能用于存储过程中。
用于检索存储过程中读取到的多行数据。可进行前进或者退后一行等操作。详细使用等用到再说。

#### 触发器：
在某些语句或事件之后后自动执行某些语句。
触发器支持的事件：
1. DELETE
2. INSERT
3. UPDATE

##### 创建触发器
需要4个信息：
* 唯一的触发器名(每个表中唯一)；
* 触发器关联的表；
* 触发器应该响应的活动（DELETE、INSERT或UPDATE）；
* 触发器何时执行（处理之前或之后）

示例：`CREATE TRIGGER newproduct AFTER INSERT ON products FOR EACH ROW SELECT 'Product added' INTO @v;` （Mysql 5后触发器不允许直接返回值，因此需要把值存放到一个变量中）
上面示例的含义：`CREATE TRIGGER`用来创建名为`newproduct`的新触发器。触发器可在一个操作发生之前或之后执行，这里给出了`AFTER INSERT`，所以此触发器将在`INSERT`语句成功执行后执行。这个触发器还指定`FOR EACH ROW`，因此代码对每个插入行执行。在这个例子中，文本'Product added'将在每次插入行后存放到v变量中。

##### 删除触发器
示例：`DROP TRIGGER newproduct;`

##### 使用触发器
**INSERT触发器**
* 在INSERT触发器代码内，可引用一个名为NEW的虚拟表，访问被插入的行；
* 在BEFOREINSERT触发器中，NEW中的值也可以被更新（允许更改被插入的值）；
* 对于AUTO_INCREMENT列，NEW在INSERT执行之前包含0，在INSERT执行之后包含新的自动生成值
示例：`CREATE TRIGGER newproduct AFTER INSERT ON products FOR EACH ROW SELECT NEW.prod_id INTO @v;`  在新插入一行后，自动将改行的`prod_id`赋值到`v`变量中。

**DELETE触发器**
* 在DELETE触发器代码内，你可以引用一个名为OLD的虚拟表，访问被删除的行
* OLD中的值全都是只读的，不能更新
将删除的行保存到另外一张表：
```sql
CREATE TRIGGER deleteorder BEFORE DELETE ON orders 
FOR EACH ROW
DEGIN
  INSERT INTO archive_orders(order_num, order_date, cust_id)
  VALUES(OLD.order_num, OLD.order_date, OLD.cust_id)
END;
```

**UPDATE触发器**
* 在UPDATE触发器代码中，你可以引用一个名为OLD的虚拟表访问以前（UPDATE语句前）的值，引用一个名为NEW的虚拟表访问新更新的值；
* 在BEFOREUPDATE触发器中，NEW中的值可能也被更新（允许更改将要用于UPDATE语句中的值）
* OLD中的值全都是只读的，不能更新

#### 管理事物处理：
事物处理可以用来维护数据库完整性，它保证成批的MySQL语句，要么完全执行，要么完全不执行。
* **事务**（transaction）指一组SQL语句；
* **回退**（rollback）指撤销指定SQL语句的过程；
* **提交**（commit）指将未存储的SQL语句结果写入数据库表；
* **保留点**（savepoint）指事务处理中设置的临时占位符（place- holder），你可以对它发布回退（与回退整个事务处理不同）

##### 控制事务处理：
开始事务标识：`START TRANSACTION;`
回退：`ROLLBACK;` 只能在执行`START TRANSACTION;`后有效
提交：`COMMIT;`将事务内执行的操作写入到表。
在`COMMIT`或`ROLLBACK`后，事务自动关闭。
设置保留点：`SAVEPOINT delete1;`在事务中增加保留点，就能回退到你想回退的保留点位置。

MySQL默认是自动提交的，即执行完一个SQL语句，自动commit。可以通过`SET autocommit=0`来取消自动提交。(`autocommit`针对的是每个连接而不是服务器)

#### 全球化和本地化：
* 字符集为字母和符号的集合；
* 编码为某个字符集成员的内部表示；
* 校对为规定字符如何比较的指令

查询系统支持字符集合默认校对：`SHOW CHARACTER SET;`
查看所有校对：`SHOW COLLATION;`
查看数据库所使用的字符集和校对：
`SHOW VARIABLES LIKE 'character%';`
`SHOW VARIABLES LIKE 'collation%';`
给表或单列指定相关字符集合校对顺序：
```sql
CREATE TABLE customers(
  cust_id      int       NOT NULL AUTO_INCREMENT,
  cust_name    char(50)  NOT NULL CHARACTER SET utf8 COLLATION utf8_general_ci,
) DEFAULT CHARACTER SET hebrew COLLATION hebrew_general_ci;
```
* 如果指定`CHARACTER SET`和`COLLATE`两者，则使用这些值。
* 如果只指定`CHARACTER SET`，则使用此字符集及其默认的校对（如`SHOW CHARACTER SET`的结果中所示）。
* 如果既不指定`CHARACTER SET`，也不指定`COLLATE`，则使用数据库默认

对`SELECT`使用`COLLATE`指定一个备用的校对顺序。

#### 安全管理
应该给不同的用户不同的访问权限，有些有完全控制权(管理员)，有些则有只读权限。
MySQL的用户账号信息都在`mysql`表中(用户的增删通常都不建议直接操作该表，而是使用相应命令)
```sql
USE mysql;
SELECT user FROM user;
```
##### 创建用户账号
`CREATE USER ben IDENTIFIED BY 'p@$$wOrd';`
也可以不增加`IDENTIFIED`，不设密码

##### 重命名用户
`RENAME USER ben TO bforta;`

##### 删除用户
`DROP USER bforta;`

##### 设置访问权限
查看用户权限:`SHOW GRANTS FOR bforta;`
不指定主机名的情况下，MySQL里`%`代表主机名，`USAGE`表示没有任何权限。
授予权限：`GRANT SELECT, INSERT ON crashcourse.* TO bforta;` 授予`bforta`用户`crashcourse`表的`SELECT` 和`INSERT`权限

##### 取消访问权限
`REVOKE SELECT ON crashcourse.* FROM bforta;` 被撤销的权限必须存在，否则出错。

##### 修改密码
`SET PASSWORD = Password('passssssword');`

##### 权限列表
|权限|说  明|
|---|---|
|ALL|除GRANTOPTION外的所有权限|
|ALTER|使用ALTER TABLE|
|ALTER ROUTINE|使用ALTER PROCEDURE和DROP PROCEDURE|
|CREATE|使用CREATE TABLE|
|CREATE ROUTINE|使用CREATE PROCEDURE|
|CREATE TEMPORARY TABLES|使用CREATE TEMPORARY TABLE|
|CREATE USER|使用CREATE USER、DROPUSER、RENAMEUSER和REVOKE ALLPRIVILEGES|
|CREATE VIEW|使用CREATE VIEW|
|DELETE|使用DELETE|
|DROP|使用DROP TABLE|
|EXECUTE|使用CALL和存储过程|
|FILE|使用SELECT INTO OUTFILE和LOAD DATA INFILE|
|GRANT OPTION|使用GRANT和REVOKE|
|INDEX|使用CREATE INDEX和DROP INDEX|
|INSERT|使用INSERT|
|LOCK TABLES|使用LOCK TABLES|
|PROCESS |使用SHOW FULL PROCESSLIST|
|RELOAD |使用FLUSH|
|REPLICATION CLIENT |服务器位置的访问|
|REPLICATION SLAVE |由复制从属使用|
|SELECT |使用SELECT|
|SHOW DATABASES |使用SHOW DATABASES|
|SHOW VIEW |使用SHOW CREATE VIEW|
|SHUTDOWN |使用mysqladminshutdown（用来关闭MySQL）|
|SUPER |使用CHANGEMASTER、KILL、LOGS、PURGE、MASTER和SETGLOBAL。还允许mysqladmin调试登录
|UPDATE |使用UPDATE|
|USAGE |无访问权限|

#### 数据库维护：
##### 数据备份
* 使用命令行实用程序`mysqldump`转储所有数据库内容到某个外部文件。在进行常规备份前这个实用程序应该正常运行，以便能正确地备份转储文件。
* 可用命令行实用程序`mysqlhotcopy`从一个数据库复制所有数据（并非所有数据库引擎都支持这个实用程序）。
* 可以使用MySQL的`BACKUP TABLE`或`SELECT INTO OUTFILE`转储所有数据到某个外部文件。这两条语句都接受将要创建的系统文件名，此系统文件必须不存在，否则会出错。数据可以用`RESTORE TABLE`来复原。
**首先刷新未写数据** 为了保证所有数据被写到磁盘（包括索引数据） ，可能需要在进行备份前使用`FLUSH TABLES`语句。

##### 进行数据库维护
* `ANALYZE TABLE`，用来检查表键是否正确。
* `CHECK TABLE`用来针对许多问题对表进行检查。在MyISAM表上还对索引进行检查。`CHECK TABLE`支持一系列的用于MyISAM表的方式。`CHANGED`检查自最后一次检查以来改动过的表。`EXTENDED`执行最彻底的检查，`FAST`只检查未正常关闭的表，`MEDIUM`检查所有被删除的链接并进行键检验，`QUICK`只进行快速扫描。
* `REPAIR TABLE`用来修复偶尔产生的MyISAM表访问产生不正确和不一致的结果。
* `OPTIMIZE TABLE`用来回收一个表删除大量数据后的空间，从而优化表性能。
  
##### 启动问题诊断
* --help显示帮助——一个选项列表；
* --safe-mode装载减去某些最佳配置的服务器；
* --verbose显示全文本消息（为获得更详细的帮助消息与--help联合使用）；
* --version显示版本信息然后退出。
更多的，可以进行网上搜索，和对应不同mysql版本进行查看。

##### 日志文件
* **错误日志**。它包含启动和关闭问题以及任意关键错误的细节。此日志通常名为hostname.err，位于data目录中。此日志名可用--log-error命令行选项更改。
* **查询日志**。它记录所有MySQL活动，在诊断问题时非常有用。此日志文件可能会很快地变得非常大，因此不应该长期使用它。此日志通常名为hostname.log，位于data目录中。此名字可以用--log命令行选项更改。
* **二进制日志**。它记录更新过数据（或者可能更新过数据）的所有语句。此日志通常名为hostname-bin，位于data目录内。此名字可以用--log-bin命令行选项更改。注意，这个日志文件是MySQL5中添加的，以前的MySQL版本中使用的是更新日志。
* **缓慢查询日志**。顾名思义，此日志记录执行缓慢的任何查询。这个日志在确定数据库何处需要优化很有用。此日志通常名为hostname-slow.log，位于data目录中。此名字可以用--log-slow-queries命令行选项更改。

#### 改善性能：
1. 首先，MySQL（与所有DBMS一样）具有特定的硬件建议。在学习和研究MySQL时，使用任何旧的计算机作为服务器都可以。但对用于生产的服务器来说，应该坚持遵循这些硬件建议。
2. 一般来说，关键的生产DBMS应该运行在自己的专用服务器上。
3. MySQL是用一系列的默认设置预先配置的，从这些设置开始通常是很好的。但过一段时间后你可能需要调整内存分配、缓冲区大小等。（为查看当前设置，可使用SHOW VARIABLES;和SHOWSTATUS;。）
4. MySQL一个多用户多线程的DBMS，换言之，它经常同时执行多个任务。如果这些任务中的某一个执行缓慢，则所有请求都会执行缓慢。如果你遇到显著的性能不良，可使用SHOWPROCESSLIST显示所有活动进程（以及它们的线程ID和执行时间）。你还可以用KILL命令终结某个特定的进程（使用这个命令需要作为管理员登录）
5. 尽量在数据库中进行过滤，而不是到客户端进行过滤。可有效减少带宽，并且数据库对过滤做了优化。
6. 总是有不止一种方法编写同一条SELECT语句。应该试验联结、并、子查询等，找出最佳的方法。
7. 使用EXPLAIN语句让MySQL解释它将如何执行一条SELECT语句。
8. 一般来说，存储过程执行得比一条一条地执行其中的各条MySQL语句快。
9. 应该总是使用正确的数据类型。
10. 决不要检索比需求还要多的数据。换言之，不要用SELECT *（除非你真正需要每个列）。
11. 有的操作（包括INSERT）支持一个可选的DELAYED关键字，如果使用它，将把控制立即返回给调用程序，并且一旦有可能就实际执行该操作。
12. 在导入数据时，应该关闭自动提交。你可能还想删除索引（包括FULLTEXT索引） ，然后在导入完成后再重建它们。
13. 必须索引数据库表以改善数据检索的性能。确定索引什么不是一件微不足道的任务，需要分析使用的SELECT语句以找出重复的WHERE和ORDERBY子句。如果一个简单的WHERE子句返回结果所花的时间太长，则可以断定其中使用的列（或几个列）就是需要索引的对象。
14. 你的SELECT语句中有一系列复杂的OR条件吗？通过使用多条SELECT语句和连接它们的UNION语句，你能看到极大的性能改进。
15. 索引改善数据检索的性能，但损害数据插入、删除和更新的性能。如果你有一些表，它们收集数据且不经常被搜索，则在有必要之前不要索引它们。（索引可根据需要添加和删除。）
16. LIKE很慢。一般来说，最好是使用FULLTEXT而不是LIKE。
17. 数据库是不断变化的实体。一组优化良好的表一会儿后可能就面目全非了。由于表的使用和内容的更改，理想的优化和配置也会改变。
18. 最重要的规则就是，每条规则在某些条件下都会被打破。

#### 其他：
服务器状态信息：`SHOW status;`
显示用户安全权限：`SHOW grants;`
显示日志信息：`SHOW errors/warnings;`


至此，本书笔记完结:tada: