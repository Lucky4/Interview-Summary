* [事务四大特性](#事务四大特性)
* [数据库的三大范式以及五大约束](#数据库的三大范式以及五大约束)
* [数据库隔离级别及每个级别会引发什么问题](#数据库隔离级别及每个级别会引发什么问题)
* [MySQL存储引擎区别以及各自的使用场景](#MySQL存储引擎区别以及各自的使用场景)
* [数据库的优化-sql语句优化和索引](#数据库的优化-sql语句优化和索引)
* [两种索引的区别](#两种索引的区别)
* [最左前缀原则-哪些情况索引会失效](#最左前缀原则-哪些情况索引会失效)
* [乐观锁悲观锁及select加排他锁](#乐观锁悲观锁及select加排他锁)
* [数据库三范式](#数据库三范式)
* [数据库的主从复制](#数据库的主从复制)
* [使用explain优化sql和索引](#使用explain优化sql和索引)
* [long_query](#long_query)
* [MVCC机制](#MVCC机制)

## 事务四大特性

[参考](https://github.com/CyC2018/CS-Notes/blob/master/notes/%E6%95%B0%E6%8D%AE%E5%BA%93%E7%B3%BB%E7%BB%9F%E5%8E%9F%E7%90%86.md#acid)

## 数据库的三大范式以及五大约束

[参考](https://www.cnblogs.com/waj6511988/p/7027127.html)

## 数据库隔离级别及每个级别会引发什么问题

[参考](https://blog.csdn.net/liuao107329/article/details/71424678)

[参考](https://github.com/CyC2018/CS-Notes/blob/master/notes/%E6%95%B0%E6%8D%AE%E5%BA%93%E7%B3%BB%E7%BB%9F%E5%8E%9F%E7%90%86.md#%E4%BA%8C%E5%B9%B6%E5%8F%91%E4%B8%80%E8%87%B4%E6%80%A7%E9%97%AE%E9%A2%98)

MySQL 实现了四个标准的隔离级别，默认级别是可重复读（REPEATABLE READ）。在可重复读隔离级别下，通过多版本并发控制（MVCC）+ 间隙锁（Next-Key Locking）防止幻影读。

## MySQL存储引擎区别以及各自的使用场景

[参考](https://blog.csdn.net/xifeijian/article/details/20316775)

## 数据库的优化-sql语句优化和索引

* EXPLAIN 分析 SELECT 查询。
* 当只要一行数据时使用 LIMIT 1。
* 减少查询字段，避免 SELECT * 语句。
* 在Join表的时候使用相当类型的例，并将其索引。
* 为经常搜索的字段建立索引，模糊查询可能另索引失效。
* 避免使用排序字段，排序字段尽量使用主键。

[参考](https://www.cnblogs.com/zhouyusheng/p/8038224.html)

[参考](https://blog.csdn.net/when_less_is_more/article/details/70187459)

## 两种索引的区别

[参考](https://github.com/CyC2018/CS-Notes/blob/master/notes/MySQL.md#mysql-%E7%B4%A2%E5%BC%95)

## 最左前缀原则-哪些情况索引会失效

[参考](https://blog.csdn.net/tengdazhang770960436/article/details/46974363)

[参考](https://blog.csdn.net/monkey_d_feilong/article/details/52291556)

## 乐观锁悲观锁及select加排他锁

[参考](https://my.oschina.net/kailuncen/blog/1504217#h2_17)

select 加共享锁：
~~~sql
SELECT ... LOCK IN SHARE MODE;
~~~

select 加排他锁：
~~~sql
SELECT ... FOR UPDATE;
~~~

## 数据库三范式

[参考](https://www.cnblogs.com/waj6511988/p/7027127.html)

## 数据库的主从复制

[参考](https://www.cnblogs.com/edisonchou/p/4133148.html)

## 使用explain优化sql和索引

[参考](https://segmentfault.com/a/1190000008131735#articleHeader0)

~~~sql
explain select * from user_info where id = 2\G
~~~
\G 的作用是将结果纵向显示。

## long_query

查看是否开启慢查询日志功能：
~~~sql
show variables like 'slow_query%';
~~~

开启慢查询日志功能：
~~~sql
set global slow_query_log=1;
~~~

设置慢查询时间阈值：
~~~sql
set global long_query_time=0.1;
~~~

## MVCC机制

[参考](https://github.com/CyC2018/CS-Notes/blob/master/notes/%E6%95%B0%E6%8D%AE%E5%BA%93%E7%B3%BB%E7%BB%9F%E5%8E%9F%E7%90%86.md#%E4%BA%94%E5%A4%9A%E7%89%88%E6%9C%AC%E5%B9%B6%E5%8F%91%E6%8E%A7%E5%88%B6)