---
layout: post
title: "python scheduler"
date: 2013-04-09 23:37
comments: true
categories: python
tags: scheduler python
---

在爬取双色球中奖号码的时候需要用到定时调度的第三方库，我就去搜了一下python scheduler，官方比较推荐的是[APScheduler],看了一下他的文档，非常简洁易懂，简单介绍一下


##调度的三种方式

###1.simple date-based scheduling(定时执行，时间固定，执行一次)

example：
需求：在 2013-1-4 13:14:21 打印 i love you

	from datetime import date
	from apscheduler.scheduler import Scheduler

	# Start the scheduler
	sched = Scheduler()
	sched.start()

	# Define the function that is to be executed
	def my_job(text):
	   print text

	# Store the job in a variable in case we want to cancel it
	#方法的第一个参数是需要执行的方法名，第二个参数是时间，第三个参数是需要执行的方法的参数列表
	job = sched.add_date_job(my_job, '2013-01-04 13:14:21', ['i love you'])

###2.Interval-based scheduling(每隔多长时间执行一次)

 example:
 需求：每隔一个小时打印一次hello world

    # Define the function that is to be executed
	job = sched.add_interval_job(my_job,hour=1,['hellow world'])

###3.cron-style scheduling(定时循环执行，比如每个月的几号，或者每周几，或者一年中的第几周执行)

example:
需求：
每周一，三，五打印`hello world` （周日是0，周六是6）
	
	#没有设置时分秒默认为0
	job = sched.add_cron_job(my_job,day-of-week='0,2,4',['hellow world'])

我在做爬虫的时候是用到的第三种调度，因为双色球大概是每周的二，四，日21点40分开奖，所以需要在周二，四，日的22点去执行任务，爬取中奖号码

	job = sched.add_cron_job(getDataFromBD,day_of_week='0,2,4',hour='22')

这里只是简单的应用，还有一种添加job方法的写法就是python的Decorator，还有一些比较高级的用法，比如线程池配置，配置jobs store到本地或者数据库的一些配置，这里就先不做介绍了


[APScheduler]:http://pythonhosted.org/APScheduler/

