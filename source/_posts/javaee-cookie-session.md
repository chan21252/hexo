---
title: JavaEE-Cookie和Session
date: 2019-04-01 15:43:43
tags:
- java
category:
- java
---

## 一、会话
### 1.1 概念

在计算机术语中，会话是指一个终端用户与交互系统进行通讯的过程，比如从输入账户密码进入操作系统到退出操作系统就是一个会话过程。会话较多用于网络上，TCP的三次握手就创建了一个会话，TCP关闭连接就是关闭会话。

Session代表服务器与浏览器的一次会话过程，这个过程是连续的，也可以时断时续的。在Servlet中，当JSP页面没有显式禁止session的时候，在打开浏览器第一次请求该jsp的时候，服务器会自动为其创建一个session，并赋予其一个sessionID，发送给客户端的浏览器。以后客户端接着请求本应用中其他资源的时候，会自动在请求头上添加：（Cookie:SESSIONID=客户端第一次拿到的session ID）。这样，服务器端在接到请求时候，就会收到session ID，并根据ID在内存中找到之前创建的session对象，提供给请求使用。

简单的说，一次会话包含多次请求和响应，浏览器第一次请求服务器时，会话建立，直到一方断开连接。

### 1.2 功能
在一次会话范围内的多次请求间，共享数据。

技术实现方式：
1. 客户端会话技术：cookie
2. 服务端会话技术：session

<!-- more -->

## 二、Cookie
### 2.1 概念
客户端会话技术，将数据保存到客户端。

Cookie（复数形态Cookies），又称为“小甜饼”。类型为“小型文本文件”，指某些网站为了辨别用户身份而储存在用户本地终端（Client Side）上的数据（通常经过加密）。

Cookie总是保存在客户端中，按在客户端中的存储位置，可分为内存Cookie和硬盘Cookie。

内存Cookie由浏览器维护，保存在内存中，浏览器关闭后就消失了，其存在时间是短暂的。硬盘Cookie保存在硬盘里，有一个过期时间，除非用户手工清理或到了过期时间，硬盘Cookie不会被删除，其存在时间是长期的。所以，按存在时间，可分为非持久Cookie和持久Cookie。

### 2.2 快速入门
1. 创建Cookie对象，绑定数据
2. response发送Cookie对象
3. request获取Cookie，读取Cookie内容

### 2.3 实现原理

> 基于响应头set-cookie和请求头cookie实现

![](http://image.5460cc.com/image/blog/javaee-cookie-session/01.jpg)

### 2.4 常见的cookie问题
#### 1.一次响应是否可以发送多个cookie？
- 可以。
- 可以创建多个Cookie对象，多次调用response.addCookie方法发送cookie即可。

#### 2.cookie在浏览器中可以保持多久？
- 默认情况，cook存储在内存中，浏览器关闭即销毁。
- 可以通过setMaxAge(int second)实现持久化存储：
  - 正数：将cookie数据写入硬盘，存储second秒。
  - 负数：默认值。
  - 0：删除该cookie。

#### 3. cookie是否能存中文
- tomcat8以前的版本，cookie不能直接存储中文，需要将中午编码（建议urlencode）。
- tomcat8开始，cookie支持中文。

#### 4. 一个tomcat服务器中，不同的web项目之间是否可以共享cookie？
- 默认情况下，不能共享
- 可以通过setPath(String path)设置cookie的获取范围，实现不同web项目共享cookie

#### 5. 不同的tomcat服务器cookie是否可以共享？
- setDomain(String path)，如果设置的一级域名相同，那么多个服务器之间的cookie可以共享。
- 比如，``setDomain(".baidu.com"); ``，那么*tieba.baidu.com*和*news.baidu.com*的cookie可以共享。

### 2.5 Cookie的特点和作用
特点：
1. cookie存储在客户端浏览器；
2. 浏览器对于单个cookie的大小有限制，对于同个域名下的cookie数量有限制。

作用：
1. cookie一般用于存储量少而不敏感的数据；
2. 在不登录的情况下，完成服务器对客户端身份的识别。

### 2.6 案例：cookie记住上一次访问的时间

#### 需求
1. 访问一个servlet，如果是首次访问，显示您好！欢迎您首次访问。
2. 如果是重复访问，显示欢迎回来，您上次访问时间为${上次访问时间}。

#### 代码实现
[https://github.com/chan21252/JavaeeLearnDemo/tree/master/01_Cookie_RememberViewTime](https://github.com/chan21252/JavaeeLearnDemo/tree/master/01_Cookie_RememberViewTime)

## 三、Session
### 3.1 概念

服务器端会话技术，在一次会话的多次请求间共享数据，将数据保存在服务器对象（HttpSession）中。

Session:在计算机中，尤其是在网络应用中，称为“会话控制”。Session 对象存储特定用户会话所需的属性及配置信息。这样，当用户在应用程序的 Web 页之间跳转时，存储在 Session 对象中的变量将不会丢失，而是在整个用户会话中一直存在下去。当用户请求来自应用程序的 Web 页时，如果该用户还没有会话，则 Web 服务器将自动创建一个 Session 对象。当会话过期或被放弃后，服务器将终止该会话。


### 3.2 快速入门
1. 获取HttpSession对象:``HttpSession session = request.getSession();``
2. 存储数据：``session.setAttribute(String s, Object o);``
3. 获取数据：``session.getAttribute(String s);``
4. 删除数据：``session.removeAttribute(String s);``

### 3.3 原理

> session的实现依赖cookie。

![](http://image.5460cc.com/image/blog/javaee-cookie-session/02.jpg)

### 3.3 常见问题
##### 1. 服务端不关闭，关闭客户端后重新打开，两次获取的session是否是同一个？
- 默认情况，不是。
- 可以通过设置键为“JESSIONID”的Cookie，并持久化，使得两次的session相同

##### 2. 客户端不关闭，服务器关闭并重启，客户端刷新后，获取的session还是同一个吗？
- 不是同一个，但是session保存的内容不变。
- tomcat会完成以下工作：
  - 服务器正常关闭前，session对象序列化到硬盘上，tomcat的work目录中。
  - 服务器启动后，session文件读取到内存的session对象中

##### 3. session什么时候被销毁？
- 服务器被关闭
- session对象调用invalidate()
- session默认失效时间 30min
  - 可以修改web.xml的配置<session-config\></session-config\>

