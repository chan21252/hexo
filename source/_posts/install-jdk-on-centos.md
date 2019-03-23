---
title: Linux安装jdk
date: 2018-12-16 12:45:23
tags:
- linux
- java
categories:
- java
---

> 本文环境 Centos6.8 64位，安装版本 jdk1.8.0_191

## 下载jdk
### 官网下载
下载地址：[https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html](https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)
选择**jdk-8u191-linux-x64.tar.gz**下载。

<!-- more -->

![下载jdk](http://image.5460cc.com/image/blog/install-jdk-on-centos/001.jpg '下载jdk')

### wget命令下载
``` bash
wget https://download.oracle.com/otn-pub/java/jdk/8u191-b12/2787e4a523244c269598db4e85c51e0c/jdk-8u191-linux-x64.tar.gz
```

## 安装
``` bash
# 解压到/opt/java/jdk1.8.0_191目录下
tar -zvxf jdk-8u191-linux-x64.tar.gz -C /opt/java/
```

## 配置jdk环境变量
``` bash
# 编辑全局变量文件
vim /etc/profile

# 在文件末尾添加如下内容：
# JAVA_HOME
export JAVA_HOME=/opt/java/jdk1.8.0_191
export JRE_HOME=$JAVA_HOME/jre
export CLASSPATH=.:$JAVA_HOME/lib/
export PATH=$PATH:$JAVA_HOME/bin

# 使全局变量文件生效
source /etc/profile
```

## 检查安装成功
``` bash
# 查看jdk版本
java -version
```
![jdk版本](http://image.5460cc.com/image/blog/install-jdk-on-centos/002.jpg '查看jdk版本')



