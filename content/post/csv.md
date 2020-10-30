---
title: "mysql导入csv文件"
date: 2013-05-20T17:13:06+08:00
draft: true
tags: ['mysql', 'csv']
categories: ["IT技术"]
---

先建立和csv相应的字段表，然后
如下命令：
```
load data infile '要导入的文件' into table 要存入的数据库表名 fields terminated by '列的分隔符' optionally enclosed by '"' escaped by '"' lines terminated by '换行符';
```
实例如：
```
load data infile 'F:/data.csv' into table attr fields terminated by ',' optionally enclosed by '"' escaped by '"' lines terminated by '\n';
```
可能出现如下错误：
```
/* SQL错误（1290）：The MySQL server is running with the --secure-file-priv option so it cannot execute this statement */
```
执行如下命令：
```
show variables like 'secure%'
```
得到这个：
+------------------+-------+
| Variable_name | Value |
+------------------+-------+
| secure_auth | ON |
| secure_file_priv | D:/wamp64/tmp/ |
+------------------+-------+
就是secure_file_priv没有正确的配置导致的报错。
所以把文件放在相应的secure_file_prive下面，再执行就可以了
```
load data infile 'D:/wamp64/tmp/data.csv' into table tp_mobileattr fields terminated by ',' optionally enclosed by '"' escaped by '"' lines terminated by '\n';
```