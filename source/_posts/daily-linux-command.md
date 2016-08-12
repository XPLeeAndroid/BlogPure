---
title: linux日常命令
date: 2016-08-10 11:30:06
tags:
- linux
---

- ps -aux | grep servers.py 查看servers.py后台进程 -aux 显示所有状态
- ps -ef | grep servers.py -e:显示所有进程，环境变量；-f:全格式 -h 不显示标题 
- kill -9 pid 强制杀死进程pid
- service iptabes stop 停止防火墙 centos7之前版本
- chkconfig iptables off 禁用防火墙 centos7之前版本
- systemctl stop firewalld.service 停止防火墙 centos7
- systemctl disable firewalld.service 禁用防火墙 centos7
- chmod -R 777 shadowsocks-rm
- mv ss-panel/{.,}* .; rmdir ss-panel 移动ss-panel文件夹下的内容到上级目录，并且删除ss-panel目录

