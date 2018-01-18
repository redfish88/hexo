---
layout: post
title: 如何优化SQL语句
date: 2013-05-28 22:39
comments: true
categories: database
tags: SQL database
---


平时写SQL时积累的一些关于SQL优化的技巧

### 选择最有效率的表名顺序
ORACLE的解析器是按照从右到左的顺序处理FROM中的表名，FROM中最后面的表（基础表）将优先被处理，在from包含多个表时，你必须将记录最少的表作为基础表，如果是3个表以上的表连接，那必须将交叉表作为基础表，交叉表是指被其他表引用的表
### 尽量用`EXISTS`和`NOT EXISTS`代替`IN`和`NOT IN`
`EXISTS`一定比`IN`查询速度快么？这个问题有过争论，根本问题是在驱动表的问题
`EXISTS`是在子查询中用到索引，而`IN`是在外查询中用到索引
e.g.
A是小表，B是大表
​	
	select * from A WHERE cc in (select cc from B)

效率低，因为用到了A上的索引，而没用上大表B的索引

	select * from A where exists(select cc from B where cc=A.cc)

效率高，因为`EXISTS`用到了B上的索引
因为`NOT IN`不会用索引，而`NOT EXISTS`还会在子查询中用到索引，所以应尽量使用`NOT EXISTS`代替`NOT IN`
### `WHERE`子句中的连接顺序
因为ORACLE采用自下而上的顺序解析SQL，所以，表连接的必须写在其他条件之前，能过滤掉最多记录的条件应该写在最后
###删除重复记录使用ROWID

	DELETE FROM STUDENT S WHERE S.ROWID > (SELECT MIN(X.ROWID) FROM STUDENT T WHERE T.ROWID = S.ROWID) 	

因为使用了ROWID，所以，是效率最高的删除重复记录的方法
### 用`EXISTS`代替`DISTINCT`
对于1对多的表信息查询时，`EXISTS`比`DISTINCT`更为迅速
e.g.
低效

	SELECT DISTINCT DEPT_ID,DEPT_NAME FROM DEPT,EMP
	WHERE DEPT.DEPT_ID = EMP.DEPT_ID

高效

	SELECT DEPT_ID,DEPT_NAME FROM DEPT D
	WHERE EXISTS(SELECT 1 FROM EMP E WHERE E.DEPT_ID = D.DEPT_ID)
### 避免在索引列上使用 !=、计算、NOT、函数、IS NULL、IS NOT NULL

以上几种情况都会导致查询中不能使用索引，因为它只会告诉你有什么而不会告诉你没有什么，ORACLE 遇到 NOT时就会与在索引列上使用函数相同，放弃使用索引而进行全表扫描

### 使用 >= 代替 >
低效

	SELECT * FROM EMP WHERE EMP_ID >= 4

高效

	SELECT * FROM EMP WHERE EMP_ID > 3

两者区别在于，前者DBMS将直接跳到 EMP_ID = 4的记录，而后者将先跳到EMP=3的记录上
###尽可能的使用`WHERE`来代替`HAVING`

避免使用HAVING子句, HAVING 只会在检索出所有记录之后才对结果集进行过滤. 这个处理需要排序,总计等操作. 如果能通过WHERE子句限制记录的数目,那就能减少这方面的开销. (非oracle中)on、where、having这三个都可以加条件的子句中，on是最先执行，where次之，having最后，因为on是先把不符合条件的记录过滤后才进行统计，它就可以减少中间运算要处理的数据，按理说应该速度是最快的，where也应该比having快点的，因为它过滤数据后才进行sum，在两个表联接时才用on的，所以在一个表的时候，就剩下where跟having比较了。在这单表查询统计的情况下，如果要过滤的条件没有涉及到要计算字段，那它们的结果是一样的，只是where可以使用rushmore技术，而having就不能，在速度上后者要慢如果要涉及到计算的字段，就表示在没计算之前，这个字段的值是不确定的，根据上篇写的工作流程，where的作用时间是在计算之前就完成的，而having就是在计算后才起作用的，所以在这种情况下，两者的结果会不同。在多表联接查询时，on比where更早起作用。系统首先根据各个表之间的联接条件，把多个表合成一个临时表后，再由where进行过滤，然后再计算，计算完后再由having进行过滤。由此可见，要想过滤条件起到正确的作用，首先要明白这个条件应该在什么时候起作用，然后再决定放在那里 

