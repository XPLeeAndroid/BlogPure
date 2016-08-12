---
title: SS用户说明
date: 2016-08-12 14:54:55
tags:
 - shadowsocks
---

# ShadowSocks使用说明 #
shadowsocks是一款开源的基于UDP转发的开源的科学上网工具
## 客户端下载与配置 ##
下载地址：[https://shadowsocks.org/en/download/clients.html](https://shadowsocks.org/en/download/clients.html "https://shadowsocks.org/en/download/clients.html")  
提供了Windows,Mac OS X ,Linux ,Android,IOS,OpenWRT的客户端  
### 配置文件 ###
Shadowsocks接收JSON格式的配置,形式如下：

    {
    "server":"my_server_ip",
    "server_port":8388,
    "local_port":1080,
    "password":"barfoo!",
    "timeout":600,
    "method":"table",
    "auth": true
	}

### 字段解释 ###

 - **server** : 您的主机名或者服务器IP地址（IPV4/IPV6）  
 - **server_port** : 服务器端口号  
 - **local_port** : 本机端口号   
 - **password** : 用于加密传输的密码  
 - **timeout** :链接超时时间，单位毫秒  
 - **method** :加密方式，“bf-cfb”,“aes-256-cfb”,"des-cfb","rc4"等。默认是table，并不安全，“aes-256-cfb”是推荐的方式 。  
 - **auth** : 一次性认证，true表示开启一次性认真特性。  

**获取账号与配置文件**

1 . **获取SS账号**  

到http://216.158.86.233/ 获取注册凭邀请码注册账号  

2 . **Windows客户端**

下载[Windows客户端](https://shadowsocks.org/en/download/clients.html)  
双击运行程序，在Windows右下角的图标区域就会多一个像纸飞机一样的图标，这个就是客户端程序  
鼠标右键点击这个纸飞机图标，会出现客户端的主菜单  
把SS账号的二维码图片显示在电脑上，在Windows的SS客户端上（注意：不是手机）点击“服务器选择”，“扫描屏幕上的二维码”， 客户端软件会识别屏幕上的二维码自动配置（电脑不需要有摄像头）

然后点击“确定”回到主菜单，选择“启用代理”和“开机启动”，让这两项前面都有对号，也就是选择上，这样客户端就配置好了。

在主菜单里面，点击代理模式，可以选择PAC模式或者全局模式，前者的意思是只有访问被墙的网站才通过SS账号访问，国内网站和国外没有被墙的网站不通过SS账号。全局模式则是所有访问都走SS。

3 . **MAC OS 系统**

[参考OSX客户端使用说明 ](https://github.com/shadowsocks/shadowsocks-iOS/wiki/Shadowsocks-for-OSX-Help)

