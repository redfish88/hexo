---
layout: post
title: "oracle mysql 分页查询"
date: 2013-03-21 21:49
comments: true
categories: database
tags: mysql oracle

---

今天面试被问到oracle、mysql的分页查询，看到这么简单的问题，还是有点窃喜的，仔细一想就有点懵了，分页？oracle？然后就在想是哪个关键字，然后就没有然后了。。。只写了一个mysql的limit，而且limit后面跟2个参数，只写了一个，突然感觉到自己底子的薄弱，兼对面试没有什么准备，错失了今天这次机会，所以下决心，从今天开始，持续更新一些面试的基本知识、面试题目，直至跳槽成功-__,-

----

* **oracle 分页查询**

完全想多了啊，oracle哪有什么关键字啊，就是`rownum`啊！！！这个可是整天用的，居然忘了分页也是它！

	select * from
	(	
		select a.*,rownum rn	 	
		from (select * from user) a 	
	)	
	where rn between 21 and 40
就是这么简单啊，如果再考虑一下效率的话
	
	select * from 
	(
		select a.*,rownum rn
		from (select * from user) a
		where rownum < 40 
	)
	where rn > 20
对比这2种写法，在绝大多数的情况下第二种写法比第一种要有效率的多，这是由于CBO优化模式下，Oracle可以将外层的查询条件推到内层查询中，以提高内层查询的执行效率。对于第二个查询语句，查询条件`where rownum < 40`会被推到内层查询中，这样Oracle查询的结果一旦超过了ROWNUM限制条件，就终止查询将结果返回了。引自[itpub]

* **mysql 分页查询**

mysql 分页查询更简单一点
	
	select * 
	from user
	order by createtime desc
	limit 21,10

limit 后面的2个参数分别代表**start point**和**how many records to display**


    







[itpub]:http://yangtingkun.itpub.net/post/468/100278