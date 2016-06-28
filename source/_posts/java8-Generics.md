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

# 为什么要用泛型 #

简言之，泛型能够使**类型**（类和接口）在定义类，接口和方法的时候参数化。非常像方法定义时用到的**形式参数**（formal parameters）,类型参数提供了一种你可以通过不同的输入来复用同一段代码的方法。不同点是，**形式参数**输入的是**值**，而类型参数输入的是**类型**。

使用泛型比非泛型有很多好处：

- 编译时更强大的类型检测


 Java编译器对泛型应用了强大的类型检测，如果代码违反了类型安全就会报错。修复编译时错误比修复运行时错误更加容易，因为运行时错误很难查找到。

- 消除类型转换(Elimination of casts)

 以下代码片段没有泛型需要转型：

    List list = new ArrayList();
	list.add("hello");
	String s = (String) list.get(0);
当我们重新用泛型编写，代码就不需要类型转换了：

    List<String> list = new ArrayList<String>();
	list.add("hello");
	String s = list.get(0);   // no cast

- 使开发者实现泛型算法

通过泛型，开发者可以自己实现泛型算法，应用到一系列的不同类型，可以自定义，并且类型安全，易读。

