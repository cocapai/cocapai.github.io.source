---
title: "Miniconca"
date: 2020-09-15T14:59:24+08:00
draft: true
---

# miniconda安装

[![img](https://raw.githubusercontent.com/cocapai/cocapai.github.io/master/uploads/4d81144e-ec2b-4e9e-9634-24bac5643ab0)](https://www.jianshu.com/u/9153eddebf9c)



### conda

conda与anaconda、miniconda的区别：

> - Conda是一个包管理器;Anaconda是一个发行包
>   conda可以理解为一个工具，也是一个可执行命令，其核心功能是包管理与环境管理。包管理与pip的使用类似，环境管理则允许用户方便地安装不同版本的python并可以快速切换
> - Anaconda是一个打包的集合器皿，里面预装好了conda、某个版本的python、众多packages、科学计算工具等等，所以也称为Python的一种发行版。也可以理解：`conda是包的管理`，可以安装包（conda install samtools），删除环境（），查找等用法。
> - 其实还有Miniconda，顾名思义，它只包含最基本的内容——python与conda，以及相关的必须依赖项，对于空间要求严格的用户，Miniconda是一种选择

### miniconda下载

1. 百度搜索conda 清华
2. 进入[Anaconda | 镜像站使用帮助 | 清华大学开源软件镜像站 | Tsinghua...](https://mirrors.tuna.tsinghua.edu.cn/help/anaconda/)
3. 找到"Miniconda 安装包可以到 https://mirrors.tuna.tsinghua.edu.cn/anaconda/miniconda/下载。
   "
4. 下载linux对应版本(服务器提前登录)：



```shell
wget -c https://mirrors.tuna.tsinghua.edu.cn/anaconda/miniconda/Miniconda2-4.5.11-Linux-x86_64.sh
```

![img](https://upload-images.jianshu.io/upload_images/10375753-9b16a677b6d8e830.png?imageMogr2/auto-orient/strip|imageView2/2/w/1006/format/webp)

miniconda不同版本_2019.1.4截图

1. 安装



```linux
# 第一步下载
# miniconda安装
# wget -c https://mirrors.tuna.tsinghua.edu.cn/anaconda/miniconda/Miniconda2-4.5.11-Linux-x86_64.sh

# 第二步安装
bash Miniconda2-4.5.11-Linux-x86_64.sh  # uname -a
# 按enter--三下空格--输入yes--按enter--输入yes
source ~/.bashrc
# 激活配置

# 第三步：配置镜像
# 配置镜像(安装一次，镜像也只配置一次，注意不要复制错误！！！)
conda config --add channels https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/free
conda config --add channels https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud/conda-forge
conda config --add channels https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud/bioconda
conda config --set show_channel_urls yes
# 执行完上述命令后，会生成~/.condarc

# 第四步：创建小环境并安装软件
# 创建名为rna的软件安装环境，同时安装python=2版本的软件
conda create -n rna python=2
# 出现三个done

# 查看当前conda环境
# 可以看到成功建立的rna
conda info --envs

# 激活/进入conda的rna环境，避免安装软件时安装到大环境
source activate rna
# 小环境创建成功，可以随便安装软件到小环境里啦

# 尝试
# 安装 sra-tools软件
conda search sra-tools  
conda install -y sra-tools  # done正确安装，且能调出软件help
......
source deactivate # 退出当前环境
```

### conda安装镜像搜索软件：

https://bioconda.github.io/recipes.html

### conda命令集合



```linux
# 查看环境名：
conda info --envs 或conda info -e
# 查看已安装软件列表：
conda list
# 退出环境：
source deactivate
# 更新：
conda update python 
# (conda将python等软件都视为package)
# 假设当前环境是python 3.4, conda会将python升级为3.4.x系列的当前最新版本

# 删除全部packages
conda remove --name/-n wes --all
# 删除某个packages
source activate wes
conda remove multiqc
# 或直接指明name
conda remove -n wes numpy
```