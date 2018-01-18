---
layout: post
title: 编程之美（一）：求连续子数组的最大和
date: 2013-04-17 23:01
comments: true
categories: 算法 java python DP 
---

最近看了不少算法相关的题目，打算把这些题目整理出来做一个系列，借用一下CSDN上[JULY]的标题吧
> 
> 输入一个整形数组，数组里有正数也有负数。

> 数组中连续的一个或多个整数组成一个子数组，每个子数组都有一个和。

> 求所有子数组的和的最大值。要求时间复杂度为**O(n)**。

e.g. 数组[1, -2, 3, 10, -4, 7, 2, -5] 和最大字数组为 3, 10, -4, 7, 2

如果没有时间复杂度的要求或许这个题可以有很多方法,但是有了时间复杂度的要求,就不能按照穷举所有子数组的方法来算了,可以来分析一下这个题目:
从第一个元素开始加,每加一次之后就要比较、记录一下最大和,如果加到某个元素的时候子数组的和为负数了，那就要放弃之前所有元素相加之和，然后从下个元素开始重新开始计算子数组的和。拿上面那个数组来表示`sum`和`max`的值的线性变化的话就是
	
	sum:	1 	-1 	 3 	 13	   9    16    18    13
	max:    1    1   3   13    13   16    18    18

最后返回`max`就是 18

用java 来实现的话就是
	
	public static int maxSum(int[] arr){
		int sum = 0;
		int max = 0;
		for(int i=0;i < arr.length; i++){
			 if(sum < 0)
			 	sum = arr[i];
			 else
			 	sum += arr[i];
			 if(max < sum)
			 	max = sum;			
		}
		return max;
	}
这样看来思路还是很清晰的，代码也不怎么复杂，最主要的是只对数组进行一次循环，也就是时间复杂度为O(N),下面来一个python版本的
	
	a = [1, -2, 3, 10, -4, 7, 2, -5]
	result = 0
	sum  = a[i]
	for i in xrange(len(a)-1):
		sum = max(a[i+1],sum+a[i+1])
		result = max(sum,result)
	    			
这个思路更牛，这是利用了ACM中的[DP]思路,据说这是DP中最简单的一类题了，(ーー゛)
说一下DP的思想：
对于列表中任何第i+1个元素，只有2种选择，作为一个新子数组的第一个元素，或者加入到前面的数组，result为已找到的最大子数组的和
这个思路是不是让你眼前一亮的感觉？真是太简洁了，动态规划（dynamic programing)的思路我这是第一次接触到，感触不小，大学没好好学，现在得补上啊

PS:DP的思想可以解决很多面试题中压轴的算法题，所以数量掌握DP还是很有必要的，以后会继续出几个利用DP去解决的算法题

本文主要引自：[JULY的博客](http://blog.csdn.net/v_july_v/article/details/6444021)









[JULY]:http://blog.csdn.net/v_JULY_v
[DP]:http://baike.baidu.com/view/28146.htm