---
title: Android拍照上传至PHP服务器并写入MySql数据库(上)
date: 2016-07-21 16:45:00
tags:
 - PHP
 - Android
---


# 需求分析 #

需求很简单，Android客户端点击拍照后，进行Base64加密，自动上传至服务器，服务器接收上传的数据并解密后保存到指定文件夹下，并将图片信心写入数据库，并返回相应的Json数据。

<!--more-->

# 数据库 #


## 创建数据库 ##


数据库名称为 turtorial_upload_image:

    CREATE DATABASE tutorial_upload_image;

## 创建表  ##

我们要保存的信息是图片的名称和图片的路径，表名称是photos：

    CREATE TABLE photos(
	id INT(32) NOT NULL AUTO_INCREMENT,
	name VARCHAR(30) NOT NULL,
	path VARCHAR(30) NOT NULL,

	PRIMARY KEY(id)
	);

# PHP #

先上代码：

    <?php
	/**
	 * Created by PhpStorm.
	 * User: Adam
	 * Date: 2016/7/21
	 * Time: 15:01
	 */
	header('Content-type ： bitmap; charset=utf-8;');
	$dbms = 'mysql';     //数据库类型
	
	$host = 'localhost'; //数据库主机名
	
	$dbName = 'tutorial_upload_image';    //使用的数据库
	
	$user = 'root';      //数据库连接用户名
	
	$pass = '';          //对应的密码
	
	$dsn = "$dbms:host=$host;dbname=$dbName";
	
	if (isset($_POST["encoding_string"])) {
    $encoding_string = $_POST["encoding_string"];

    $image_name = $_POST["image_name"];

    //decode 客户端上传的base64数据
    $decoded_string = base64_decode($encoding_string);

    $path = "images/" . $image_name;  //定义存放路径
    $file = fopen($path, "wb");

    $is_written = fwrite($file, $decoded_string);
    fclose($file);
    //写入数据库
    if ($is_written > 0) {
        //sql语句
        $strSql = "insert into photos(name,path) values('$image_name','$path')";


        //方法一 pdo方式  支持多种数据库
        try {

            $dbh = new PDO($dsn, $user, $pass); //初始化一个PDO对象
            $temp = $dbh->prepare($strSql);
            $temp->execute();
            $array = array(
                "status" => true,
                "msg" => "插入数据成功"
            );
            echo json_encode($array);
            $dbh = null; //运行完成后关闭链接
        } catch (PDOException $e) {
            $array = array(
                "status" => false,
                "msg" => "插入数据失败" . ($e->getMessage())
            );
            echo json_encode($array);

        }


        //方法2  mysqli方式
	//        $connection=mysqli_connect('localhost','root','','tutorial_upload_image');
	//        $result=mysqli_query($connection,$strSql);
	//        if($result){
	//            $array = array (
	//                "status" => true,
	//                "msg" => "插入数据成功"
	//            );
	//            echo json_encode($array);
	//        }else{
	//            $array = array (
	//                "status" => false,
	//                "msg" => "插入数据失败"
	//            );
	//            echo json_encode($array);
	//        }
	//        mysqli_close($connection);   //关闭连接
    }
	
	}
	?>	


这里我列举了两种连接数据库的方式 PDO和mysqli  ，mysql方式官方已经不推荐使用了，所以就不再列举（我也没用过）。数据库连接过程首先是读取POST过来的内容，然后写到images目录下面，再把地址和名称写入数据库里，返回相应的JSON数据，在这里每次上传完毕后都关闭了连接。

# 测试 #

测试阶段我们找用postman进行模拟发送数据，首先在[这个网站](http://www.motobit.com/util/base64-decoder-encoder.asp)Base64 encode一张图片，复制之后，拷贝到postman里面，postman数据如下：

![](/img/android_upload_img_to_php_1.png)

测试结果成功~

下节我们将开发安卓客户端。



