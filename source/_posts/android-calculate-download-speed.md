---
title: 安卓计算下载速度
date: 2016-07-28 11:23:13
tags:
 - android
---

昨天开始封装一个安卓多线程下载器，在写的过程中，猜测想加入检测下载过程中的速度，于是google一番，得出一个比较靠谱的答案，在此总结一下。
<!--more-->

# NANOSECONDS #

**NANOSECONDS**，毫微秒，十亿分之一秒，1s=1000000000毫微秒。

# CODE EXAMPLE#

           long start = System.nanoTime();   //开始时间
                long totalRead = 0;  //总共下载了多少
                final double NANOS_PER_SECOND = 1000000000.0;  //1秒=10亿nanoseconds
                final double BYTES_PER_MIB = 1024 * 1024;    //1M=1024*1024byte
                while (((len = is.read(buffler, 0, 1024)) >0)) {
                    totalRead += len;
                    double speed = NANOS_PER_SECOND / BYTES_PER_MIB * totalRead / (System.nanoTime() - start + 1);
	}

# WARNING #

这种方法计算的是从start开始时间的平均速度，不是实时速度。


