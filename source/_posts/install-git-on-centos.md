---
title: Linux源代码安装git
date: 2018-12-09 12:54:01
author: chan
tags:
- linux
- git
categories:
- git
---
> 本文安装环境 Centos6.8 64位，安装版本 git-2.19.2

## 一、下载git源代码
github：[https://github.com/git/git/releases](https://github.com/git/git/releases)
官网：[http://git-scm.com/download](http://git-scm.com/download)

<!-- more -->

## 二、安装依赖工具
git的工作需要调用 curl，zlib，openssl，expat，libiconv 等库的代码，所以需要先安装这些依赖工具。
Centos安装有yum，可以使用以下命令
``` bash
yum install curl-devel expat-devel gettext-devel \
  openssl-devel zlib-devel
```

## 三、创建git用户添加root权限
``` bash
# 新建用户
useradd git

# sudoers文件添加写入、执行权限
chmod 740 /etc/sudoers

# 修改sudoers文件
vim /etc/sudoers
# 在/etc/sudoers找到root    ALL=(ALL)       ALL，
# 在下面添加一行：git    ALL=(ALL)       ALL

# sudoers文件恢复只读权限
chmod 440 /etc/sudoers

```

## 四、编译安装

安装包上传到/opt目录，然后解压安装。

``` bash
# 解压
tar -zvxf git-2.19.2.tar.gz

# 编译安装
cd git-2.19.2
make prefix=/opt/git all
sudo make prefix=/opt/git install
```

安装成功后会在/opt目录下出现/git文件夹。

## 五、git配置环境变量
为了便于使用git命令，还需要将git添加到环境变量中。
``` bash
# 修改环境变量配置文件
vim /etc/profile

# 在文件末尾添加
# GIT_HOME
export GIT_HOME=/opt/git/bin
export PATH=$PATH:$GIT_HOME

# 输出环境变量
export $PATH

# 使配置文件修改生效
source /etc/profile
```

## 六、git命令

### 1、查看git版本，确认安装和环境变量配置成功。
``` bash
git --version
```

### 2、初始化仓库
``` bash
# 在/home/git下新建文件夹
cd /home/git
mkdir Hellogit
cd Hellogit/

# 初始化仓库
git init
```
