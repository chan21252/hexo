---
title: Linux源代码安装nodejs
date: 2018-12-09 16:01:44
tags:
- linux
- nodejs
categories:
- linux
---

## 下载
官网：[http://nodejs.cn/download/](http://nodejs.cn/download/)
``` bash
wget http://cdn.npm.taobao.org/dist/node/v10.14.1/node-v10.14.1.tar.gz
```

## 安装依赖工具
``` bash
yum install python gcc make g++ wget
```

## 解压编译安装

``` bash
#解压
tar -zvxf node-v10.14.1.tar.gz

# 编译&安装
cd node-v10.14.1/
./configure --prefix=/opt/nodejs
make prefix=/opt/nodejs
make prefix=/opt/nodejs install
```

### 编译报错
``` bash
which: no python2.7 in (/usr/local/sbin:/usr/local/bin:/sbin:/bin:/usr/sbin:/usr/bin:/opt/git/bin:/root/bin)
ERROR: Did not find a new enough assembler, install one or build with
       --openssl-no-asm.
       Please refer to BUILDING.md
```

### 安装python2.7
``` bash
#下载
cd /usr/local/src
curl https://www.python.org/ftp/python/2.7.14/Python-2.7.14.tgz -o python-2.7.14.tgz

# 解压
tar -zxvf Python-2.7.14.tgz
cd Python-2.7.14
./configure

# 编译&安装
make
make install

#查看版本
python --version
```

## 查看版本
``` bash
node -v
npm -v
```

