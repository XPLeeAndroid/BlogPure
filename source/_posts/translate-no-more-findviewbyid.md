---
title: 翻译:no more findViewById
date: 2016-06-26 14:40:57
tags:
 - android
 - 翻译
---
> - 原文链接 [https://medium.com/google-developers/no-more-findviewbyid-457457644885#.cs0jg2og6](https://medium.com/google-developers/no-more-findviewbyid-457457644885#.cs0jg2og6)
> - 翻译： [Adamin90](https://github.com/adamin1990)
> - 转载请注明出处，谢谢！


# No More findViewById #
******

Android Studio开发android程序的一个小特点是数据绑定。我会在将来的文章中讲解它的其他一些优雅的特点，但是你要了解的最基础的是怎样消除findViewById.
<!--more-->
{% codeblock lang:java %}
TextView hello = (TextView) findViewById(R.id.hello);
{% endcodeblock %}

虽然现在有很多试用的方法可以省略这些多余代码，但是Android Studio 1.5以及更高版本已经有官方的方法了。

首先，你必须在Application的build.gradle里的android块内填写如下代码：
    
    android {
    …
    dataBinding.enabled = true
}

下一步就是在你的layout文件的最外层添加 <layout>标签，不管你用的是任何 ViewGroup:
    
    <layout xmlns:android="http://schemas.android.com/apk/res/android"
        xmlns:tools="http://schemas.android.com/tools">
    <RelativeLayout
            android:layout_width="match_parent"
            android:layout_height="match_parent"
            android:paddingLeft="@dimen/activity_horizontal_margin"
            android:paddingRight="@dimen/activity_horizontal_margin"
            android:paddingTop="@dimen/activity_vertical_margin"
            android:paddingBottom="@dimen/activity_vertical_margin"
            tools:context=".MainActivity">

        <TextView
                android:id="@+id/hello"
                android:layout_width="wrap_content"
                android:layout_height="wrap_content"/>

    </RelativeLayout>
</layout>

**Layout**标签告诉Android Studio这个layout在编译时将进行额外的操作，查找到所有感兴趣的view,并且标签为下一步。所有外部没有包`layout`标签的布局将不会执行额外操作。所以你可以在新项目中少量使用而无需改变项目中其他的部分。

下面要做的就是告诉它在运行时分别加载你的layout。因为它向后兼容，所以不需要依赖新框架的改变来加载这些预执行的layout文件。因此你只需对程序做一个轻微的改变。

从一个Activity,不是：

{% codeblock lang:java %}
    setContentView(R.layout.hello_world);
TextView hello = (TextView) findViewById(R.id.hello);
hello.setText("Hello World"); 
{% endcodeblock %}

而是这样加载：

    HelloWorldBinding binding = 
    DataBindingUtil.setContentView(this, R.layout.hello_world);
    binding.hello.setText("Hello World"); 

你可以看到 **HelloWordBinding**这个类自动为**hello_world.xml**生成并且id为“@+id/hello”的view分配到了一个**hello**的field你可以使用。没有强制类型转换，没有findViewById.

这标兵这是访问view的机制不仅仅比findViewById更加简单，而且也更加快！绑定程序一次执行覆盖所有layout的view，把view分配到field。当你运行findViewById，的时候view结构每次都会被遍历查找。

你会注意到一件事:它对你的变量名使用了驼峰命名法（比如hello_world.html 变成类 HelloWorldBinding）,所以如果你给它的id是“@+id/hello_text”,那么field的名称将会是 helloText.

当你正在inflate你布局里RecyclerView,ViewPager，或其他不设置Activity内容的控件，你将希望在生成的类上用生成的类型安全的方法，这里有几个版本匹配LayoutInflater,所以使用你最适合食用的.举个例子：

    HelloWorldBinding binding = HelloWorldBinding.inflate(
    getLayoutInflater(), container, attachToContainer);

如果你们有把被inflate的view 附加到包含他们的ViewGroup上，你必须访问被infalte的view的view结构。你可以用binding的getRoot()方法：

    linearLayout.addView(binding.getRoot());

现在，你可能会考虑，如果我有一个layout包含同步view的不同配置呢？layout预执行和运行时inflate阶段通过添加所有View 的id到生成的类，如果没有被inflate的话设置为null。

相当神奇，不是吗？最好的部分是运行时没有反射和其他任何高消耗的技术。把他少量添加到你现有程序里面非常容易，他能让你的生活更加简单，让你的layout加载的更快！