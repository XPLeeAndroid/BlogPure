---
title: java8教程-泛型（Generics）
date: 2016-06-28 14:04:35
tags:
 - java
---
> - 原文链接 [https://docs.oracle.com/javase/tutorial/java/generics/index.html)
> - 翻译： [Adamin90](https://github.com/adamin1990)
> - 转载请注明出处，谢谢！

# 泛型（已更新） #

 在任何繁琐的（nontrivial）软件项目中，bug是家常便饭。细心的规划，编程和测试可以帮助减少bug的普遍性（pervasiveness）,但是无论如何，无论在哪里，bug总会伺机悄悄溜进（creep）你的代码，因为很明显，新的特性会不断的被引入，并且你的代码基数会不断变大和复杂。
  
  幸运的是，一些bug相比其它比较容易检测。编译时bug可以在早期被检测到；你可以利用编译器的错误信息查明是什么问题并且解决，就在那时。然而，运行时bug会更加未预知,他们不会立即展示出来，不知道什么时候发生，可能根本不在程序真正出现问题的点上。

泛型通过更多的在编译时检测bug为你的代码增加了稳定性。
