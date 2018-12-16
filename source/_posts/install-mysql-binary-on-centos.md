---
title: Linux二进制文件安装mysql
date: 2018-12-12 22:46:27
tags:
- linux
- mysql
categories:
- mysql
---
> 本文环境Centos6.8 64位，安装版本Mysql5.7.24

## 下载软件
### 官网下载
官网下载地址：[https://dev.mysql.com/downloads/mysql/](https://dev.mysql.com/downloads/mysql/)
选择如下文件下载：
![mysql下载文件选择](http://pjj1fddwq.bkt.clouddn.com/image/blog/install-mysql-binary-on-centos/001.jpg 'mysql下载文件选择')

<!-- more -->

### wget下载：
``` bash
# root用户执行
cd ~
wget https://cdn.mysql.com//Downloads/MySQL-5.7/mysql-5.7.24-linux-glibc2.12-x86_64.tar.gz
```
![wget下载](http://pjj1fddwq.bkt.clouddn.com/image/blog/install-mysql-binary-on-centos/002.jpg)

## 创建mysql用户
```
groupadd mysql
useradd -r -g mysql mysql
```

## 安装
### 解压缩
``` bash
# 解压到/opt目录下，重命名文件夹
tar -zvxf mysql-5.7.24-linux-glibc2.12-x86_64.tar.gz -C /opt
mv /opt/mysql-5.7.24-linux-glibc2.12-x86_64 /opt/mysql-5.7.24
```
![](http://pjj1fddwq.bkt.clouddn.com/image/blog/install-mysql-binary-on-centos/003.jpg)

### 查看系统是否已安装mysql
```
rpm -qa | grep -i mysql
```
![查看是否已安装mysql](http://pjj1fddwq.bkt.clouddn.com/image/blog/install-mysql-binary-on-centos/004.jpg)
如图本系统并未安装mysql

### 配置数据库目录
``` bash
mkdir -p /opt/mysql-5.7.24/{data,log,etc,run,tmp}
touch /opt/mysql-5.7.24/etc/my.cnf
touch /opt/mysql-5.7.24/log/{mysql_error.log,mysql_bin.log,mysql_slow_query.log}
touch /opt/mysql-5.7.24/run/mysql.sock
touch /opt/mysql-5.7.24/run/mysql.pid
```
目录说明：
1. 数据目录             /opt/mysql-5.7.24/data
2. 配置文件             /opt/mysql-5.7.24/etc/my.cnf
3. 错误日志             /opt/mysql-5.7.24/log/mysql_error.log
4. 二进制日志           /opt/mysql-5.7.24/log/mysql_bin.log
5. 慢查询日志           /opt/mysql-5.7.24/log/mysql_slow_query.log
6. 套接字socket文件     /opt/mysql-5.7.24/run/mysql.sock
7. pid文件              /opt/mysql-5.7.24/run/mysql.sock

### 修改文件夹拥有者为mysql
```
chown -R mysql:mysql /opt/mysql-5.7.24
```

### 设置环境变量
``` bash
vim /etc/profile

# 在文件末尾追加如下内容
# MYSQL_HOME
export MYSQL_HOME=/opt/mysql-5.7.24
export PATH=$PATH:$MYSQL_HOME/bin

source /etc/profile
```

### 编辑my.cnf配置文件
部分参数填写的路径要与前面设置的mysql路径一致，配置文件如下：

``` bash
# vim /opt/mysql-5.7.24/etc/my/cnf
# 内容同步至/etc/my.cnf
[client]
port    = 3306
socket  = /opt/mysql-5.7.24/run/mysql.sock
default-character-set = utf8mb4

[mysqld_safe]
socket		= /opt/mysql-5.7.24/run/mysql.sock
nice		= 0

[mysqld]
#
# * Base Setting
#
pid_file        = /opt/mysql-5.7.24/run/mysql.pid
socket          = /opt/mysql-5.7.24/run/mysql.sock
port            = 3306
user            = mysql
basedir         = /opt/mysql-5.7.24
datadir         = /opt/mysql-5.7.24/data
tmpdir          = /opt/mysql-5.7.24/tmp
lc-messages-dir = /usr/share/mysql
skip-external-locking

#
# Instead of skip-networking the default is now to listen only on
# localhost which is more compatible and is not less secure.
# bind-address		= 127.0.0.1

#
# * Fine Tuning
#
key_buffer_size		    = 16M
max_allowed_packet	    = 16M
thread_stack		    = 192K
thread_cache_size       = 8
# This replaces the startup script and checks MyISAM tables if needed
# the first time they are touched
myisam-recover-options  = BACKUP
#max_connections        = 100
#table_cache            = 64
#thread_concurrency     = 10

#
# * Query Cache Configuration
#
query_cache_limit	= 1M
query_cache_size    = 16M

#
# * Logging and Replication
#
# Both location gets rotated by the cronjob.
# Be aware that this log type is a performance killer.
# As of 5.1 you can enable the log at runtime!
#general_log_file        = /var/log/mysql/mysql.log
#general_log             = 1
#
# Error log - should be very few entries.
#
log_error = /opt/mysql-5.7.24/log/mysql_error.log
#
# Here you can see queries with especially long duration
slow_query_log = on
slow-query-log-file = /opt/mysql-5.7.24/mysql_slow_query.log
#long_query_time    = 2
#log-queries-not-using-indexes
#
# The following can be used as easy to replay backup logs or for replication.
# note: if you are setting up a replication slave, see README.Debian about
#       other settings you may need to change.
#server-id		= 1
#log_bin			= /opt/mysql-5.7.24/log/mysql_bin.log
expire_logs_days    = 10
max_binlog_size     = 100M
#binlog_do_db		= include_database_name
#binlog_ignore_db	= include_database_name

#
# * InnoDB
#
# InnoDB is enabled by default with a 10MB datafile in /var/lib/mysql/.
# Read the manual for more InnoDB related options. There are many!
#
# * Security Features
#
# Read the manual, too, if you want chroot!
# chroot = /var/lib/mysql/
#
# For generating SSL certificates I recommend the OpenSSL GUI "tinyca".
#
# ssl-ca=/etc/mysql/cacert.pem
# ssl-cert=/etc/mysql/server-cert.pem
# ssl-key=/etc/mysql/server-key.pem

symbolic-links=0
```

### 初始化
```
mysqld --initialize --user=mysql --basedir=/opt/mysql-5.7.24 --datadir=/opt/mysql-5.7.24/data
```
- 系统会生成一个**临时密码**记得保存；
- 如果出现错误，查看/opt/mysql-5.7.24/log/mysql_error.log日志。

安装结果如图：
![](http://pjj1fddwq.bkt.clouddn.com/image/blog/install-mysql-binary-on-centos/006.jpg)

## mysql配置系统服务
### 修改mysqld文件
```
# 拷贝mysql.server到/etc/init.d/mysqld文件
cp /opt/mysql-5.7.24/support-files/mysql.server /etc/init.d/mysqld

#设置mysqld文件中的路径
vim /etc/init.d/mysqld

# 设置basedir和datadir
basedir=/opt/mysql-5.7.24
datadir=/opt/mysql-5.7.24/data
```
![](http://pjj1fddwq.bkt.clouddn.com/image/blog/install-mysql-binary-on-centos/007.jpg)

### 加入服务
```
chkconfig --add mysqld
```

### 设置开机启动和查看设置状态
```
chkconfig mysqld on
chkconfig mysqld --list
```

### 启动mysql服务
```
# 可选参数 start|stop|restart
service mysqld start
```
我在这里遇到了错误
![](http://pjj1fddwq.bkt.clouddn.com/image/blog/install-mysql-binary-on-centos/008.jpg)

> Starting MySQL...The server quit without updating PID file [FAILED]sql-5.7.24/data/iZuf65pgqo8iyipci611e6Z.pid).

原因是*/opt/mysql-5.7.24/run/*文件夹下的*mysql.pid*和*mysql.sock*没有执行权限
```
chmod -R 755 /opt/mysql-5.7.24/run/
```

### 查看mysql服务状态
```
service mysqld status
```
![](http://pjj1fddwq.bkt.clouddn.com/image/blog/install-mysql-binary-on-centos/009.jpg 'mysql服务正常启动')

## 登录mysql，修改root密码
``` bash
mysql -u root -p
Enter password: [输入临时密码]
mysql> ALTER USER 'root'@'localhost' IDENTIFIED BY '新密码';
mysql> flush privileges;
mysql> exit;

# 重新登录
mysql -u root -p
Enter password: [新密码]
```
![](http://pjj1fddwq.bkt.clouddn.com/image/blog/install-mysql-binary-on-centos/010.jpg)

End
















