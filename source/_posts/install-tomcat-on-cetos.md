---
title: Linux安装tomcat
date: 2018-12-16 13:07:04
tags:
- linux
- java
- tomcat
categories:
- java
---

> 本文环境 Centos6.8 64位，安装版本 tomcat8.5.35

## 环境依赖
tomcat运行需要jvm（java虚拟机），因此需确认系统已经安装jdk。
参考文档：[Linux安装jdk](http://blog.5460cc.com/2018/12/16/install-jdk-on-centos/)

如下是tomcat和jdk的版本对应关系：
![tomcat-jdk](http://image.5460cc.com/image/blog/install-tomcat-on-centos/002.jpg)
官网文档：[http://tomcat.apache.org/whichversion.html](http://tomcat.apache.org/whichversion.html)

<!-- more -->

## 下载软件
### 官网下载
下载地址：[https://tomcat.apache.org/download-80.cgi](https://tomcat.apache.org/download-80.cgi)

![下载tomcat](http://image.5460cc.com/image/blog/install-tomcat-on-centos/001.jpg '下载tomcat')

### wget下载
``` bash
wget https://www-us.apache.org/dist/tomcat/tomcat-8/v8.5.35/bin/apache-tomcat-8.5.35.tar.gz
```

## 安装
``` bash
# 解压到/opt/tomcat目录下
tar -zvxf apache-tomcat-8.5.35.tar.gz -C /opt/tomcat

# 启动tomcat,运行startup脚本
/opt/tomcat/apache-tomcat-8.5.35/bin/startup.sh
```
查看/logs目录下的**catalina.{yyyy-mm-dd}.log**日志文件，出现如下信息启动成功：
![tomcat启动日志](http://image.5460cc.com/image/blog/install-tomcat-on-centos/003.jpg)


## 配置启动脚本
### 修改catalina.sh脚本

``` bash
vim /opt/tomcat/apache-tomcat-8.5.35/bin/catalina.sh
```

在文件头部添加如下内容：
``` bash
# Tomcat service
#chkconfig:2345 10 90
#description:Tomcat service

export CATALINA_HOME=/opt/tomcat/apache-tomcat-8.5.35
export JAVA_HOME=/opt/java/jdk1.8.0_191
```

![](http://image.5460cc.com/image/blog/install-tomcat-on-centos/004.jpg)

chkconfig 2345 10 90：分别定义了tomcat的启动级别、关闭和启动优先级。
CATALINA_HOME：tomcat目录
JAVA_HOME：jdk目录

## 添加系统服务

``` bash
# 添加启动脚本到服务脚本文件夹
ln -s /opt/tomcat/apache-tomcat-8.5.35/bin/catalina.sh /etc/init.d/tomcat

# 添加到系统服务
chkconfig --add tomcat

# 设置开机启动
chkconfig tomcat on
```

## 服务命令
``` bash
# 启动服务
service tomcat start

# 停止服务
service tomcat stop
```

> 控制台输出的信息无法确定tomcat是否成功启动/停止，请查看日志文件

## 配置防火墙端口
tomcat默认端口是8080，配置防火墙打开对应端口。
``` bash
vim /etc/sysconfig/iptables
-A INPUT -m state --state NEW -m tcp -p tcp --dport 8080 -j ACCEPT

# 重启防火墙
service iptables restart
```

如果是阿里云服务器，还需要在安全组规则中开启8080端口。

## 访问tomcat服务器
tomcat默认网页目录是../apache-tomcat-8.5.35/webapp。
在浏览器中输入**ip:8080**，出现如下页面，则tomcat访问成功。
![](http://image.5460cc.com/image/blog/install-tomcat-on-centos/005.jpg)








