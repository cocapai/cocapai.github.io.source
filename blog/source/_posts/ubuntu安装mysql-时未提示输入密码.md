---
title: ubuntu安装mysql 时未提示输入密码
date: 2019-09-24 17:10:35
tags:
---



我在Ubuntu16.04版本中使用终端安装MySQL5.7时，按照度娘的教程，搜索如何安装，大多是如下代码：

 sudo apt-get install mysql-server
 sudo apt-get install mysql-client

检查MySQL是否运行：

　sudo netstat -tap | grep mysql

如果成功安装，我的会显示如下内容：

tcp        0      0 localhost:mysql         *:*                     LISTEN      18475/mysqld 

PS：重启/打开/关闭MySQL的方法是：sudo service mysql restart/start/stop

就这两个命令就安装好了，可是我在安装过程中并没有出现要我写用户名和密码的地方，我一脸懵逼，
完成后在终端输入mysql -u root -p之后，要求我输入密码，可是我并不知道密码，随便输入之后，

ERROR 1045 (28000): Access denied for user 'root'@'localhost' (using password: YES)

百度了三五个小时，解决方案五花八门，我最后使用有效的方法是：
打开一个文件

 sudo vim /etc/mysql/debian.cnf

在这个文件里面有着MySQL默认的用户名和用户密码，
最最重要的是：用户名默认的不是root，而是debian-sys-maint，如下所示

# Automatically generated for Debian scripts. DO NOT TOUCH!
[client]
host     = localhost
user     = debian-sys-maint
password = hGu99nJgoWcmCDKT
socket   = /var/run/mysqld/mysqld.sock
[mysql_upgrade]
host     = localhost
user     = debian-sys-maint
password = hGu99nJgoWcmCDKT
socket   = /var/run/mysqld/mysqld.sock
basedir  = /usr

密码会随即给一个很复杂的，这个时候，要进入MySQL的话，就是需要在终端把root更改为debian-sys-maint，如下代码

mysql -u debian-sys-maint -p
1
然后终端会提示你输入密码

Enter password:
1
这是输入文件中的密码即可成功登陆。
当然了，这之后就要修改密码了，毕竟密码太难记。

经过度娘的指导，我所安装的版本是5.7，所以password字段已经被删除，取而代之的是authentication_string字段，所以要更改密码：

mysql> update mysql.user set authentication_string=password('password'), plugin='mysql_native_password' where user='root'and Host = 'localhost';

如果显示：

Query OK, 1 row affected, 1 warning (0.00 sec)
Rows matched: 1  Changed: 1  Warnings: 1

则代表成功修改，之后需要*重启**MySQL，方可登录成功。
顺便说一下删除MySQL的方法，省的之后再找度娘。
代码如下：

sudo apt-get autoremove --purge mysql-server-5.7
sudo apt-get remove mysql-server
sudo apt-get autoremove mysql-server
sudo apt-get remove mysql-common

上面的可能会有些是多余的，之后需要清理残余数据

 dpkg -l |grep ^rc|awk '{print $2}' |sudo xargs dpkg -P

