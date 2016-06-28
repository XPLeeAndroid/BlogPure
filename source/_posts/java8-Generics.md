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
<!--more-->
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

# 泛型类型 #

**泛型类型**是泛型类或者接口被类型参数化。下面的**Box**类将被更改演示这个概念。

### 简单的 Box 类 ###

列举一个简单的非泛型 **Box**操作任意类型的object。它只需要提供两个方法：set，添加一个obejct到box，get,获取这个对象：

    public class Box {
    private Object object;

    public void set(Object object) { this.object = object; }
    public Object get() { return object; }
    }
因为它的方法接收或返回一个对象，你可以任意传入，只要传入的不是原始数据类型。我们没有在编译时辨别clas如何使用的。一边可能替换一个 Integer到box，另一边获取的不是Integer类型，而可能传入一个String类型，结果会导致运行时错误。

### 泛型版本的Box ###

泛型类的定义形式如下：

    class name<T1, T2, ..., Tn> { /* ... */ }

类型参数部分被一对尖括号（<>）划分，紧跟类名，它指定了**类型参数**（也叫作类型变量）T1， T2， ....,和Tn.

把原Box类更新为泛型类，你要通过把“public class Box”改变为“public class Box<T>”创建一个类型声明。这会引入一个**类型变量**, **T**,你可以在类中任意地方使用。通过这个改变，Box类就变为：

    /**
 	* Generic version of the Box class.
 	* @param <T> the type of the value being boxed
 	*/
	public class Box<T> {
    // T stands for "Type"
    private T t;

    public void set(T t) { this.t = t; }
    public T get() { return t; }
	}

你可以看到，所有**Object**出现的地方都被替换为T了。一个类型变量可以指定为任意非原始类型的类型：任意的类，任意的接口，任意的数组，甚至其他的类型变量。同样的技术可以应用到创建泛型接口上。

### 类型参数命名规则（Naming Conventions） ###

通过规则，类型参数是单独的，大写字母。这个表示鲜明区别了你已知的变量命名规则，一个好的理由是：没有这个规则，你将很难区分类型变量和原生类或接口名的区别。

最普遍使用的类型参数是：

- E -Element（Java Collections框架大量使用）
- K -Key
- N -Number
- T -Type
- V -Value 
- S,U,V 等 -第二，第三，第四个类型

你可以在JAVA SE API 看到这些名字的使用。

### 调用和实例化一个泛型类型  ###
要在你的代码引用泛型类 Box，你必须执行 泛型类型调用，把T替换成具体的值，比如Integer： 

    Box<Integer> integerBox;

你可以认为泛型类型调用跟原生方法调用大致一样，但是不是传入一个参数到方法，而是传入一个类型蚕食--这个情况下的Integer--给Box类本身。
> **Type Parameter**和**Type Argument**术语（Terminology）：
> 很多开发者交换使用这个两个术语，但是这两个术语并不同。敲代码时，
> type argument 创建一个参数化类型，因此，Foo< T>中的T是type parameter，Foo< String> f中的String是一个type argument。

就想其他的变量定义，上面的代码不会真正创建一个新的 Box对象。它只是声明，integerBox将持有一个“Box of Integer”的引用，用以读取Box<Integer>.泛型类型的调用通常称为参数化类型。

为了实例化这个类，用new 关键字，把<Integer>放在类名和括号之间。

    Box<Integer> integerBox = new Box<Integer>();

### The Diamond ###
在Java SE 7及以后版本，可以省去类型参数调用泛型类的构造函数，用一个空的类型参数（<>）,编译器可以通过上下文决定，或推测type arguments，这个尖括号非正式得叫作diamond(钻石？这么奇葩)，你可以这样创建Box< Integer>的一个实例：

    Box<Integer> integerBox = new Box<>();
要查看更多关于diamond 符号和类型推断（inference）,请看[类型推断](https://docs.oracle.com/javase/tutorial/java/generics/genTypeInference.html)。

### 多类型参数 ###
正如前面提到的，泛型类可以有多个类型参数。比如泛型 OrderedPair 类，实现了泛型接口 Pair：

    public interface Pair<K, V> {
    public K getKey();
    public V getValue();
	}

	public class OrderedPair<K, V> implements Pair<K, V> {

    private K key;
    private V value;

    public OrderedPair(K key, V value) {
	this.key = key;
	this.value = value;
    }

    public K getKey()	{ return key; }
    public V getValue() { return value; }
	}
下面的语句创建了两个OrderedPair的实例：


    Pair<String, Integer> p1 = new OrderedPair<String, Integer>("Even", 8);
	Pair<String, String>  p2 = new OrderedPair<String, String>("hello", "world");

new OrderedPair<String,Integer>把K实例化为String，V实例化为Integer。因此OrderedPair的参数类型分别(respectively)是String和Integer。因为[自动装箱](https://docs.oracle.com/javase/tutorial/java/data/autoboxing.html)，传入String和int到类是有效的。

### 参数化类型###

你也可以用一个参数化的类型（ie List< String>）替换（substitute）类型参数（K ，V）,例如用OrderedPair< K,V>:

    OrderedPair<String, Box<Integer>> p = new OrderedPair<>("primes", new Box<Integer>(...));




    
