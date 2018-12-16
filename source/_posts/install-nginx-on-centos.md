---
title: Linux源代码安装nginx
date: 2018-12-09 13:49:48
tags:
- linux
- nginx
categories:
- linux
---

> 本文安装环境 Centos6.8 64位，安装版本 nginx 1.14.2

## 一、下载源代码
官网：[http://nginx.org/en/download.html](http://nginx.org/en/download.html)

``` bash
wget http://nginx.org/download/nginx-1.14.2.tar.gz
```
<!-- more -->

## 二、安装依赖工具
yum安装
``` bash
yum -y install gcc gcc-c++ autoconf automake zlib zlib-devel openssl openssl-devel pcre
```

手动安装依赖，举例pcre库
``` bash
cd /usr/local/src
wget https://ftp.pcre.org/pub/pcre/pcre-8.36.tar.gz
tar -zxvf pcre-8.36.tar.gz
cd pcre-8.36
./configure
make
make install
```

## 三、编译和安装
``` bash
# 解压
tar -zvxf nginx-1.14.2.tar.gz

# 编译和安装
cd nginx-1.14.2
./configure --prefix=/opt/nginx 
make prefix=/opt/nginx
make prefix=/opt/nginx install
```

## 四、启动nginx
``` bash
/opt/nginx/sbin/nginx
```

访问浏览器，输入IP，显示“Welcome to nginx!”。

## 五、添加nginx到系统服务
### 1、编写nginx文件
在/etc/init.d目录下编写nginx文件
```bash
vim /etc/init.d/nginx
```

nginx文件内容如下
``` bash
#!/bin/bash
# nginx Startup script for the Nginx HTTP Server
# this script create it by caffreyxin at 2007.10.15.
# it is v.0.0.1 version.
# if you find any errors on this scripts, please contact caffreyxin.
# and send mail to xinyflove at sina dot com.
#
# chkconfig: - 85 15
# description: Nginx is a high-performance web and proxy server.
#              It has a lot of features, but it's not for everyone.
# processname: nginx
# pidfile: /var/run/nginx.pid
# config: /usr/local/nginx/conf/nginx.conf

# nginx directory
nginxd=/opt/nginx/sbin/nginx
nginx_config=/opt/nginx/conf/nginx.conf
nginx_pid=/var/run/nginx.pid

RETVAL=0
prog="nginx"

# Source function library.
. /etc/rc.d/init.d/functions

# Source networking configuration.
. /etc/sysconfig/network

# Check that networking is up.
[ ${NETWORKING} = "no" ] && exit 0

[ -x $nginxd ] || exit 0


# Start nginx daemons functions.
start() {

    if [ -e $nginx_pid ];then
        echo "nginx already running...."
        exit 1
    fi

    echo -n $"Starting $prog: "
    daemon $nginxd -c ${nginx_config}
    RETVAL=$?
    echo
    [ $RETVAL = 0 ] && touch /var/lock/subsys/nginx
    return $RETVAL
}


# Stop nginx daemons functions.
stop() {
    echo -n $"Stopping $prog: "
    killproc $nginxd
    RETVAL=$?
    echo
    [ $RETVAL = 0 ] && rm -f /var/lock/subsys/nginx /var/run/nginx.pid
}


# reload nginx service functions.
reload() {

    echo -n $"Reloading $prog: "
    #kill -HUP `cat ${nginx_pid}`
    killproc $nginxd -HUP
    RETVAL=$?
    echo

}

# See how we were called.
case "$1" in
start)
        start
        ;;

stop)
        stop
        ;;

reload)
        reload
        ;;

restart)
        stop
        start
        ;;

status)
        status $prog
        RETVAL=$?
        ;;
*)
        echo $"Usage: $prog {start|stop|restart|reload|status|help}"
        exit 1
esac

exit $RETVAL
```

### 2、添加到系统服务
``` bash
# 添加到系统服务
chkconfig --add /etc/init.d/nginx
chmod 755 /etc/init.d/nginx
chkconfig --add nginx

# 设置开机自启动
/sbin/chkconfig --level 345 nginx on
```

### 3、启动、停止、无间断重启
``` bash
# 可选 start | stop | restart | reload | status |  help
service nginx start
service nginx stop
service nginx reload
```