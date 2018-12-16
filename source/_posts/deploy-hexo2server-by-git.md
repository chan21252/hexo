---
title: git自动部署hexo博客到服务器
date: 2018-12-09 18:25:36
tags:
- linux
- git
- hexo
categories:
- git
---

## 环境搭建

- 本地(Windows)
    - node.js
    - hexo
- 远程服务器(Linux)
    - git
    - nginx

相关文章：
- [Linux安装git](http://blog.5460cc.com/2018/12/09/install-git-on-centos/)
- [Linux安装nginx](http://blog.5460cc.com/2018/12/09/install-nginx-on-centos/)

<!-- more -->

## git设置
### git命令添加软连接
如果git没有安装在/usr/bin/git，需要为/git/bin目录下的命令添加软连接。否则，同步时会出现*git-receive-pack:command not found*错误。

``` bash
# 我的git安装在了/opt目录下
ln -s /opt/git/bin/git-upload-pack /usr/bin/git-upload-pack
ln -s /opt/git/bin/git-upload-archive /usr/bin/git-upload-archive
ln -s /opt/git/bin/git-shell /usr/bin/git-shell
ln -s /opt/git/bin/git-receive-pack /usr/bin/git-receive-pack
ln -s /opt/git/bin/gitk /usr/bin/gitk
ln -s /opt/git/bin/git-cvsserver /usr/bin/git-cvsserver
ln -s /opt/git/bin/git /usr/bin/git
```

### 初始化git仓库
``` bash
# 切换到git 用户
su git

# 创建仓库
cd /home/git
mkdir hexo.git

#初始化仓库
cd hexo.git
git init --bare
```

## 配置SSH
将本地的公钥复制到**/home/git/.ssh/authorized_keys**文件中。

### 测试SSH登录
``` bash
ssh -i [公钥路径] -p [端口号] git@服务器ip
```
### 创建SSH config文件
如果服务器修改了SSH默认端口，可以在本地的.ssh目录下创建config文件，方便hexo deploy时访问指定端口。
配置文件内容如下：
```
Host 服务器ip
HostName 服务器ip
User git
Port SSH端口号
IdentityFile ~/.ssh/git_rsa
```

## 配置git Hooks
### 创建post-receive文件
``` bash
cd /home/git/hexo.git/hooks
vim post-receive
```

### 编辑文件内容
```
#!/bin/bash
GIT_REPO=/home/git/hexo.git
TMP_GIT_CLONE=/tmp/hexo
PUBLIC_WWW=/var/www/html
rm -rf ${TMP_GIT_CLONE}
git clone $GIT_REPO $TMP_GIT_CLONE
rm -rf ${PUBLIC_WWW}/*
cp -rf ${TMP_GIT_CLONE}/* ${PUBLIC_WWW}
```

### 确认文件权限
post-receive文件中提到了三个目录：
- /home/git/hexo.git
- /tmp/hexo
- /var/www/html

需确保以上三个目录和目录中所有的文件拥有者为**git**。
可以通过**chown**命令修改拥有者。
``` bash
sudo chown git:git -R /var/www
sudo chown git:git -R /home/git/hexo.git
```

## 配置本地hexo
修改本地hexo目录下的_config.ym，修改deploy配置：
```
# Deployment
## Docs: https://hexo.io/docs/deployment.html

deploy:
  - type: git
    repo: git@github.com:<username>/<username>.github.io.git
    branch: master
  - type: git
    repo: <username>@服务器ip:hexo.git
    branch: master
```

## 同步
本地执行```hexo d```命令，博客成功同步到服务器的/var/www/html中，则搭建成功。







