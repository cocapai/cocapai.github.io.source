---
title: "Docker的坑"
date: 2020-06-04T16:19:02+08:00
draft: true
tags: ["docker"]
categoies: ["IT技术"]
---
1、查看你当前的内核版本
```
uname -r
```
2、更新yum包
```
sudo yum update
```
3、卸载已安装的docker（如果安装过的话）
```
 yum remove docker  docker-common docker-selinux docker-engine
```
4、安装需要的软件包
```
sudo yum install -y yum-utils device-mapper-persistent-data lvm2
```
5、设置yum源
```
sudo yum-config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo
```
6、可以查看所有仓库中所有docker版本，并选择特定版本安装
```
yum list docker-ce --showduplicates | sort -r
```
7、重新安装docker
```
sudo yum install docker-ce
```
8、启动docker
```
sudo systemctl start docker
```
9、验证安装是否成功
```
docker virsion  //查看docker的版本
docker ps //查看当前正在运行的容器
```
10、删除原本装好的nginx容器（如果安装过）
```
docker rm nginx(容器名字或者id)
```
11、删除nginx镜像文件
```
docker images      //查看镜像文件
docker rmi nginx   //删除nginx镜像
docker rmi -f nginx     强制删除nginx镜像
```
12、重装并启动nginx
```
docker run --name nginx -d -p 80:80 nginx
```
13、安装成功，测试成功