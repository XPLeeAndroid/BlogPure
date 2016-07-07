---
title: PHP学习笔记
date: 2016-07-07 11:33:29
tags:
  - PHP


---

## php文件操作的模式 ##
- **r** 只读权限打开文件
- **w** 只写权限打开文件，擦除内容，如果没有则新建文件
- **a** 只写权限打开文件
- **x** 新建一个只写权限的文件
- **r+** 打开读/写权限文件
- **w+** 打开读/写权限的文件， 擦除内容，如果没有则新建文件
- **a+** 打开读/写权限文件，如果没有则新建文件；
- **x+** 新建一个读/写权限的文件

## 将表单提交到本身页面 ##

只需将form的action 设置为`<?php echo $_SERVER['PHP_SELF'];?>` 
为了防止跨站点攻击(XSS)，我们通常需要使用**htmlspecialchars()**
避免**$_SERVER["PHP_SELF"]** 被利用。如下：

    <form method="post" action="<?php echo htmlspecialchars($_SERVER["PHP_SELF"]);?>">

