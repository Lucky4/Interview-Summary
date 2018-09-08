* [一、了解 SQL](#了解-SQL)
* [二、MySQL 简介](#MySQL-简介)
* [三、检索数据](#检索数据)
* [三、排序检索数据](#排序检索数据)
* [四、过滤数据](#过滤数据)


## 了解SQL
数据库：保存有组织的数据的容器（注意与数据库管理系统的区别）。

表：某种特定类型数据的结构化清单。

模式：关于数据库和表的布局及特性的信息。

列：表中的一个字段。每个列都有一个数据类型，它限制了该列中存储的数据。

行：表中的一个记录。

主键：表中的一个列，其值能够唯一区分表中每个行。作为主键需要满足两个条件：
* 主键唯一
* 主键不允许为空

SQL：结构化查询语言，是一种专门用来与数据库通信的语言。
<br>

## MySQL 简介
MySQL是一种数据库关系系统（DBMS），即是一种数据库软件。

DBMS可分为两类：
* 基于共享文件系统的DBMS。
* 基于客户机-服务器的DBMS。
<br>

## 检索数据

#### DISTINCT 关键字
此关键字指示MySQL返回不同的值。它应用于所有的列而不仅仅是它的前置列,也就是说所有列的值都相同才算相同。

~~~sql
SELECT DISTINCT vend_id
FROM products;
~~~

#### LIMIT 关键字
限制返回结果行数。有两个参数，第一个参数为开始位置，从0开始；第二个参数为要检索的行数。

返回前5行:
~~~sql
SELECT prod_name
FROM products
LIMIT 5;
~~~

返回6~10行：
~~~sql
SELECT prod_name
FROM products
LIMIT 5, 5;
~~~
<br>

## 排序检索数据
子句：SQL语句由子句构成。如SELECT，WHERE等。

#### ORDER BY 子句
ORDER BY子句取一个或多个列的名字，据此对输出进行排序。

根据一个列进行排序,如果一个列相同，那么根据其他列进行排序：
~~~sql
SELECT prod_id, prod_price, prod_name
FROM products
ORDER BY prod_price, prod_name;
~~~

#### 排序方向
* DESC 降序
* ASC  升序（默认）

在多个列上进行排序，需要要在每个列指定关键字。
~~~sql
SELECT prod_id, prod_price, prod_name
FROM products
ORDER BY prod_price DESC, prod_name ASC;
~~~
<br>

## 过滤数据

可以使用WHERE子句对搜索的数据进行SQL过滤。数据也可以在应用层上过滤，但是这样服务器不得不通过网络发送多余的数据，这将导致网络带宽的浪费。

~~~sql
SELECT prod_name
FROM products
WHERE prod_price = 2;
~~~