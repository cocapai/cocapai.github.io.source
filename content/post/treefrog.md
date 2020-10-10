---
title: "基于Ｑt/C++Ｗeb后台MVC框架TreeFrog（环境搭建）"
date: 2018-08-29T12:36:35+08:00
draft: true
tag: ["c++","Treefrog"]
categories: ["工作"]
cover: "https://www.cocapai.com/uploads/20181117220632243.png"
---

## Treefrog 入门

#### 从入门到放弃系列之 Treefrog

官方网站： http://www.treefrogframework.org/en/download/ 
这里环境是Linux，下面所有的命令都针对Linux环境。

参考官方文档：http://www.treefrogframework.org/en/user-guide/tutorial/

**1 .　安装TreeFrog Framework框架的依赖库：**

```
sudo apt-get install -y qt5-default qt5-qmake libqt5sql5-mysql libqt5sql5-psql  libqt5sql5-odbc libqt5sql5-sqlite libqt5core5a libqt5qml5 libqt5xml5 qtbase5-dev qtdeclarative5-dev qtbase5-dev-tools gcc g++ make1
```

**2 .　下载TreeFrog Framework源码及安装**

```
ubuntu@VM-0-5-ubuntu:~/treefrog$ wget https://github.com/treefrogframework/treefrog-framework/archive/v1.21.0.tar.gz
...
ubuntu@VM-0-5-ubuntu:~/treefrog$ tar xvf v1.21.0.tar.gz
...
ubuntu@VM-0-5-ubuntu:~/treefrog$ cd treefrog-framework-1.21.0/
ubuntu@VM-0-5-ubuntu:~/treefrog/treefrog-framework-1.21.0$ ./configure 
Reading /home/ubuntu/treefrog/treefrog-framework-1.21.0/tools/tfmanager/tfmanager.pro
Reading /home/ubuntu/treefrog/treefrog-framework-1.21.0/tools/tfserver/tfserver.pro
Reading /home/ubuntu/treefrog/treefrog-framework-1.21.0/tools/tmake/tmake.pro
Reading /home/ubuntu/treefrog/treefrog-framework-1.21.0/tools/tspawn/tspawn.pro

Compiling MongoDB driver library ... OK

First, run "make" and "sudo make install" in src directory.
Next, run "make" and "sudo make install" in tools directory.
ubuntu@VM-0-5-ubuntu:~/treefrog/treefrog-framework-1.21.0$ cd src
ubuntu@VM-0-5-ubuntu:~/treefrog/treefrog-framework-1.21.0/src$ make
...
ubuntu@VM-0-5-ubuntu:~/treefrog/treefrog-framework-1.21.0/src$ sudo make install
ubuntu@VM-0-5-ubuntu:~/treefrog/treefrog-framework-1.21.0/src$ cd ../tools
ubuntu@VM-0-5-ubuntu:~/treefrog/treefrog-framework-1.21.0/tools$ make
...
ubuntu@VM-0-5-ubuntu:~/treefrog/treefrog-framework-1.21.0/src$ sudo make install1234567891011121314151617181920212223
```

**3 . 生成项目骨架**

```
ubuntu@VM-0-5-ubuntu:~/treefrog/pro$ tspawn new blogapp1
```

**4 . 创建数据库** 
这里以sqlite为例吧，mysql是一样的

```
ubuntu@VM-0-5-ubuntu:~/treefrog/pro/blogapp$ sqlite3 db/blogdb
SQLite version 3.11.0 2016-02-15 17:29:24
Enter ".help" for usage hints.
sqlite> CREATE TABLE blog (id INTEGER PRIMARY KEY AUTOINCREMENT, title VARCHAR(20), body VARCHAR(200), created_at TIMESTAMP, updated_at TIMESTAMP, lock_revision INTEGER);1234
```

created_at //创建时间，会由TreeFrog负责自动插入和更新 
updated_at //修改时间，会由TreeFrog负责自动插入和更新 
lock_revisio //会被TreeFrog识别为乐观锁

**所谓乐观锁，其实就是在更新数据（写操作）时不主动进行获得锁并上锁的操作，而是在最后保存写入时再通过检查对象数据的版本号，如果与原来的一致（说明期间没有其他人进行更新），那才执行最后的写入操作，否则放弃执行。TreeFrog的ORM框架支持乐观锁机制，这需要对应的数据表提供一个数据列来表示版本号（一般是整型）。这样就可以省去大量的上锁操作，可以提高系统的整体效率。**

**5 . 配置数据库**

```
ubuntu@VM-0-5-ubuntu:~/treefrog/pro/blogapp$ vi config/database.ini

[dev]
DriverType=QSQLITE
DatabaseName=db/blogdb
HostName=
Port=
UserName=
Password=
ConnectOptions=
...
ubuntu@VM-0-5-ubuntu:~/treefrog/pro/blogapp$ tspawn --show-tables
DriverType:   QSQLITE
DatabaseName: db/blogdb
HostName:     
Database opened successfully (说明数据库配置成功)
-----------------
Available tables:
  blog
  sqlite_sequence

ubuntu@VM-0-5-ubuntu:~/treefrog/pro/blogapp$ 12345678910111213141516171819202122
```

**6 . 根据数据库表自动生成源代码**

```
ubuntu@VM-0-5-ubuntu:~/treefrog/pro/blogapp$ tspawn scaffold blog1
```

根据之前创建好的数据表blog自动生成C++源代码——包括控制器、模型、视图

**7 . 编译blogapp项目**

```
ubuntu@VM-0-5-ubuntu:~/treefrog/pro/blogapp$ qmake -r "CONFIG+=release"
ubuntu@VM-0-5-ubuntu:~/treefrog/pro/blogapp$ make12
```

编译成功，将会在lib目录下生成以下库文件

```
ubuntu@VM-0-5-ubuntu:~/treefrog/pro/blogapp$ ll lib/
total 356
drwxrwxr-x  2 ubuntu ubuntu   4096 May  2 15:05 ./
drwxrwxr-x 16 ubuntu ubuntu   4096 May  2 14:31 ../
lrwxrwxrwx  1 ubuntu ubuntu     22 May  2 15:05 libcontroller.so -> libcontroller.so.1.0.0*
lrwxrwxrwx  1 ubuntu ubuntu     22 May  2 15:05 libcontroller.so.1 -> libcontroller.so.1.0.0*
lrwxrwxrwx  1 ubuntu ubuntu     22 May  2 15:05 libcontroller.so.1.0 -> libcontroller.so.1.0.0*
-rwxrwxr-x  1 ubuntu ubuntu  78432 May  2 15:05 libcontroller.so.1.0.0*
lrwxrwxrwx  1 ubuntu ubuntu     18 May  2 14:44 libhelper.so -> libhelper.so.1.0.0*
lrwxrwxrwx  1 ubuntu ubuntu     18 May  2 14:44 libhelper.so.1 -> libhelper.so.1.0.0*
lrwxrwxrwx  1 ubuntu ubuntu     18 May  2 14:44 libhelper.so.1.0 -> libhelper.so.1.0.0*
-rwxrwxr-x  1 ubuntu ubuntu   7496 May  2 14:44 libhelper.so.1.0.0*
lrwxrwxrwx  1 ubuntu ubuntu     17 May  2 15:05 libmodel.so -> libmodel.so.1.0.0*
lrwxrwxrwx  1 ubuntu ubuntu     17 May  2 15:05 libmodel.so.1 -> libmodel.so.1.0.0*
lrwxrwxrwx  1 ubuntu ubuntu     17 May  2 15:05 libmodel.so.1.0 -> libmodel.so.1.0.0*
-rwxrwxr-x  1 ubuntu ubuntu 123712 May  2 15:05 libmodel.so.1.0.0*
lrwxrwxrwx  1 ubuntu ubuntu     16 May  2 15:05 libview.so -> libview.so.1.0.0*
lrwxrwxrwx  1 ubuntu ubuntu     16 May  2 15:05 libview.so.1 -> libview.so.1.0.0*
lrwxrwxrwx  1 ubuntu ubuntu     16 May  2 15:05 libview.so.1.0 -> libview.so.1.0.0*
-rwxrwxr-x  1 ubuntu ubuntu 125784 May  2 15:05 libview.so.1.0.0*1234567891011121314151617181920
```

**8 . 启动blogapp项目**

```
ubuntu@VM-0-5-ubuntu:~/treefrog/pro/blogapp$ treefrog -e dev

//后台启动
ubuntu@VM-0-5-ubuntu:~/treefrog/pro/blogapp$ treefrog -e -d dev
//正常停止程序
ubuntu@VM-0-5-ubuntu:~/treefrog/pro/blogapp$ treefrog -k stop
//强制停止程序
ubuntu@VM-0-5-ubuntu:~/treefrog/pro/blogapp$ treefrog -k abort
//重启程序
ubuntu@VM-0-5-ubuntu:~/treefrog/pro/blogapp$ treefrog -k restart12345678910
```

**9 . 浏览器访问测试** 
http://localhost:8800/Blog/index