---
title: 《mysql必知必会》读书笔记
date: 2020-11-05 16:11:48
categories: 笔记
tags: [mysql]
---

mysql初学者报道(•̀⌄•́)
　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　——　by JiHan
* * *
*以下内容都是根据书中的内容，以及自己觉得有价值的部分进行记录。*

<!-- more -->

# 《mysql必知必会》读书笔记

## 了解mysql
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
  
**SQL**是一种专门用来与数据库通信的语言
**外键**

## MySQL简介
分为客户端(mysql)，和服务端(mysqld)。

##  使用MySQL
**先看本书的目录，进行环境安装和基础表的导入** [下载](./《mysql必知必会》读书笔记/mysql_scripts.zip)
linux，只用命令行。
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
**帮助**:
`HELP cmd;`
`HELP SHOW;`
**数据库创建选择**：
数据库创建：`CREATE DATABASE name;`
数据库选择：`USE name;`
数据库信息查看：`SHOW DATABASES;`
**表相关**：
表列表：`SHOW tables;`
列显示：`SHOW columns FROM table_name;`
检索单个列：`SELECT prod_name FROM table_name;`（没有明确排序的情况，顺序不能保证一致性。）
检索多个列：`SELECT prod_name1,  prod_name2 FROM table_name;`
检索所有列：`SELECT * FROM table_name \G;` (\G是更加友好是输出方式)
检索列值不同的行：`SELECT DISTINCT prod_name1 FROM table_name;` (不会显示prod_name1值相同的行)
检索多个列：`SELECT prod_name1,  prod_name2 FROM table_name;`
**其他**：
服务器状态信息：`SHOW status;`
显示用户安全权限：`SHOW grants;`
显示日志信息：`SHOW errors/warnings;`
