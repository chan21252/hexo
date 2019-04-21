---
title: JavaEE-Servlet基础
date: 2019-03-27 10:05:37
tags:
- java
category:
- java
---

## 一、Servlet
### 1.1 概念
Server applet，运行在服务器端的小程序。狭义的Servlet是指Java语言实现的一个接口，广义的Servlet是指任何实现了这个Servlet接口的类，一般情况下，人们将Servlet理解为后者。Servlet运行于支持Java的应用服务器中。从原理上讲，Servlet可以响应任何类型的请求，但绝大多数情况下Servlet只用来扩展基于HTTP协议的Web服务器。

<!-- more -->

### 1.2 快速入门
1. 创建JavaEE项目
2. 定义一个类，实现Servlet接口
3. 实现接口中的抽象方法
4. 配置Servlet

### 1.3 示例
定义Servlet类
``` java
package com.chan21252.helloservlet;

import javax.servlet.*;
import javax.servlet.annotation.WebServlet;
import java.io.IOException;
import java.io.PrintWriter;

/**
 * MyServlet
 * 实现Servlet接口
 */
public class MyServlet implements Servlet {

    @Override
    public void init(ServletConfig servletConfig) throws ServletException {
        
    }

    @Override
    public ServletConfig getServletConfig() {
        return null;
    }

    @Override
    public void service(ServletRequest servletRequest, ServletResponse servletResponse) throws ServletException, IOException {
        System.out.println("HelloServlet");
    }

    @Override
    public String getServletInfo() {
        return "My Servlet";
    }

    @Override
    public void destroy() {

    }
}

```

配置servlet，在web.xml中
``` xml
<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xmlns="http://xmlns.jcp.org/xml/ns/javaee"
         xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/javaee http://xmlns.jcp.org/xml/ns/javaee/web-app_3_1.xsd"
         version="3.1">
    <servlet>
        <servlet-name>MyServlet</servlet-name>
        <servlet-class>com.chan21252.helloservlet.MyServlet</servlet-class>
    </servlet>
    <servlet-mapping>
        <servlet-name>MyServlet</servlet-name>
        <url-pattern>/my</url-pattern>
    </servlet-mapping>
</web-app>
```

启动tomcat，在浏览器中输入http://localhost:8080/项目名/my，控制台输出“HelloServlet”

### 1.4 Servlet执行原理

1. 浏览器访问http://localhost:8080/项目名/my，http://localhost:8080为tomcat容器
2. 在tomcat容器中找到项目名对应的项目，解析web.xml
3. web.xml中找到*url-pattern*为 /my 的Servlet，是MyServlet
4. 找到MyServlet对应的类是com.chan21252.helloservlet.MyServlet
5. tomcat将MyServlet全类名对应的class文件加载进内存，`` Class.forName; ``，创建对象：`` cls.newInstance(); ``
6. tomcat调用Servlet类方法

### 1.5 Servlet的生命周期

1. 被创建：执行init方法，只执行一次
   1. 指定Servlet的创建时机，在<servlet\>标签下配置<load-on-startup\>
       <load-on-startup\> \>= 0：在服务器启动时被创建
       <load-on-startup\> < 0：在第一次访问是被创建 
   2. Servlet的init只执行一次，说明在Servlet在内存中只有一个对象，Servlet是单例的。
       多个用户同时访问，可能存在线程安全问题。
       解决：尽量不要在Servlet中定义成员变量。
2. 提供服务：执行service方法，每次调用执行
   1. 每次被访问，执行service方法。
3. 被销毁：执行destroy方法，只执行一次
   1. 服务器关闭时，Servlet被销毁，销毁之前执行destroy方法，一般同于释放资源。
   2. 只有服务器正常关闭时，才会执行destroy方法。

### 1.6 Servlet3.0
1. Servlet3.0项目可以不需要web.xml
2. Servlet3.0支持使用注解配置Servlet

``` java
//Servlet注解配置
@WebServlet(
        name = "ServletDemo",
        urlPatterns = "/demo",
        initParams = {
                @WebInitParam(name = "admin", value = "chan21252"),
                @WebInitParam(name = "email", value = "chanjobs21252@gmail.com")
        }
)
```

urlPatterns：Servlet的访问路径
1. 支持多个路径，@WebServlet(urlPatterns = {url1, url2, ...})
2. 路径定义规则
    1. /xxx
    2. /xxx/.../xxx 多层路径
    3. *.do|action

### 1.7 Servlet体系结构
Servlet 接口
    |
GnenericServlet 抽象类，实现了除Service以外的方法
    |
HttpServlet抽象类 对http协议进行封装，service方法根据请求类型来执行doGet/doPost方法。 

建议Servlet的写法：继承HttpServlet类，复写doGet/doPost方法

## 二、Http
### 2.1 概念

HTTP协议（HyperText Transfer Protocol，超文本传输协议）是因特网上应用最为广泛的一种网络传输协议，所有的WWW文件都必须遵守这个标准。
HTTP是一个基于TCP/IP通信协议来传递数据（HTML 文件, 图片文件, 查询结果等）。

特点：
1. 基于TCP/IP的高级协议
2. 默认端口号：80
3. 基于请求/响应模型
4. 无状态的协议：每次请求之间相互独立，后续请求无法得到前面请求的信息

- HTTP是无连接：无连接的含义是限制每次连接只处理一个请求。服务器处理完客户的请求，并收到客户的应答后，即断开连接。采用这种方式可以节省传输时间。
- HTTP是媒体独立的：这意味着，只要客户端和服务器知道如何处理的数据内容，任何类型的数据都可以通过HTTP发送。客户端以及服务器指定使用适合的MIME-type内容类型。
- HTTP是无状态：HTTP协议是无状态协议。无状态是指协议对于事务处理没有记忆能力。缺少状态意味着如果后续处理需要前面的信息，则它必须重传，这样可能导致每次连接传送的数据量增大。另一方面，在服务器不需要先前信息时它的应答就较快。

历史版本：
1.0：每一次请求都会新建链接
1.1：复用链接

### 2.2 请求消息数据格式
#### 请求行
格式：`` 请求方式 url 请求协议/版本 ``，举例：`` GET /login.html HTTP/1.1 ``

http协议有7中请求方式，常用的有2种GET/POST
1. GET：
  - 请求参数在url后
  - 请求的url长度有限制
2. POST：
  - 请求参数在请求体中
  - 请求的url长度不限制

#### 请求头
格式：
请求头：请求头值

常见的请求头
1. User-Agent：浏览器版本信息
2. Referer：告诉从请求从哪来，作用防盗链，统计工作


#### 请求空行
空行

#### 请求体（正文）
封装POST请求参数

#### 请求消息数据示例
```
GET / HTTP/1.1
Host: www.baidu.com
Connection: keep-alive
Pragma: no-cache
Cache-Control: no-cache
Upgrade-Insecure-Requests: 1
User-Agent: Mozilla/5.0 (Windows NT 10.0; WOW64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/72.0.3626.96 Safari/537.36
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/webp,image/apng,*/*;q=0.8
Accept-Encoding: gzip, deflate, br
Accept-Language: zh-CN,zh;q=0.9,en;q=0.8

username=chan21252
```

### 2.3 响应消息数据格式
1. 响应行
   1. 格式 `` 协议/版本 响应状态码 状态码描述
2. 响应头
3. 响应空行
4. 响应体：传输的数据

## 三、Request
### request对象和response对象的原理
1. request对象和response对象是服务器创建的，供开发使用
2. request对象用来获取请求消息，response对象用来设置响应消息

![](http://image.5460cc.com/image/blog/servlet-base/01.jpg)

### request对象继承关系
javax.servlet.ServletRequest 接口
    |
javax.servlet.http.HttpServletRequest 接口
    |
org.apache.cataline.connector.RequestFacade 类(tomcat)

### request功能
1. 获取请求消息数据
   1. 请求行数据
   2. 请求头数据
   3. 请求体数据
2. 其他功能
   1. 获取请求参数通用方式
   2. 请求转发
        服务器内部资源跳转，浏览器url地址不变，转发使用的是同一个请求
   3. 共享数据
        - 域对象：一个有作用范围的对象，可以在范围内共享数据
        - request域：一次请求的范围，用于请求转发多个资源中共享数据
   4. 获取ServletContext

``` java
package com.chan21252.helloservlet;

import javax.servlet.ServletException;
import javax.servlet.annotation.WebServlet;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import java.io.IOException;

/**
 * request对象功能演示
 */
@WebServlet(
        name = "RequestServletDemo",
        urlPatterns = "/demo/request-demo"
)
public class RequestServletDemo" extends HttpServlet {

    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        //1.获取请求行数据
        String method = req.getMethod();                //请求方式
        String contextPath = req.getContextPath();      //虚拟目录
        String servletPath = req.getServletPath();      //servlet路径
        String queryString = req.getQueryString();      //请求参数
        String requestUri = req.getRequestURI();        //请求URI
        StringBuffer requestUrl = req.getRequestURL();  //请求URL
        String protocol = req.getProtocol();            //http协议及版本
        String ip = req.getRemoteAddr();                //客户端IP

        System.out.println(method);
        //省略输出语句...

        //2.获取请求头数据
        Enumeration<String> headerNames = req.getHeaderNames();

        while (headerNames.hasMoreElements()){
            String name = headerNames.nextElement();
            String value = req.getHeader(name);

            System.out.println(name + ":\t" + value);
        }
    }

    @Override
    protected void doPost(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        //3.获取请求体，只有post方式有请求体
        /*
         * 1.获取流对象
         * BufferReader getReader()
         * ServletInputStream getInputStream()
         */
        BufferedReader br = req.getReader();
        while (br != null){
            System.out.println(br.readLine());
        }

        //获取请求参数
        String username = req.getParameter("username");
        String[] choice = req.getParameterValues("choice");
        Enumeration<String> parameterNames = req.getParameterNames();
        Map<String, String[]> parameterMap = req.getParameterMap();

        //共享数据
        req.setAttribute("msg", "hello");
        req.getAttribute("msg");
        req.removeAttribute("msg");
        
        //请求转发
        req.getRequestDispatcher("/demo/forward").forward(req, resp);
    }

}
```

## 四、Response
### 4.1 功能：
1. 设置响应行
   1. 设置响应状态码：`` resp.setStatus(200); ``
2. 设置响应头
   1. 设置响应头属性值: `` resp.setHeader(String name, String value); ``
3. 设置响应体
   1. 返回数据给浏览器
      1. 输出字节流: `` ServletOutputStream getOutputStream(); ``
      2. 输出字符流：`` PrintWriter getWriter(); ``
4. 重定向：`` resp.sendRedirect(String url); ``
   1. 浏览器地址改变
   2. 重定向可以访问其他服务器的资源
   3. 重定向是两次请求，无法使用request对象来共享数据

## 五、ServletContext
概念：代表整个web应用，可以和servlet容器（tomcat）来通信。

功能：
1. 获取MIME类型
   1. MIME：互联网通信过程中定义的文件数据类型，格式 大类/小类型 text/html，image/jpeg
   2. 获取：`` String getMimeType(String file); ``
2. 共享数据（作用域：整个应用）：
   1. ``setAttribute(String name, Object value);``
   2. ``getAttribute(String name);``
   3. ``removeAttribute(String name);``
3. 获取文件的真实路径（服务器路径）:`` servletContext.getRealPath(String filePath) ``

获取ServletContext对象：
1. `` request.getServletContext(); ``
2. `` this.getServletContext(); ``

获取服务器的资源
`` servletContext.getRealPath(String file) ``

## 六、Filter过滤器

### 6.1 概念：
Servlet 过滤器可以动态地拦截请求和响应，以变换或使用包含在请求或响应中的信息。

可以将一个或多个 Servlet 过滤器附加到一个 Servlet 或一组 Servlet。Servlet 过滤器也可以附加到 JavaServer Pages (JSP) 文件和 HTML 页面。调用 Servlet 前调用所有附加的 Servlet 过滤器。

Servlet 过滤器是可用于 Servlet 编程的 Java 类，可以实现以下目的：

在客户端的请求访问后端资源之前，拦截这些请求。
在服务器的响应发送回客户端之前，处理这些响应。

### 6.2 快速入门：
1. 定义一个类实现Filter接口
2. 重写方法
    3. 设置放行：``filterChain.doFilter(request, response) ``
3. 配置拦截路径
    1. web.xml
    2. 注解 `` @WebFilter("/") ``

### 6.3 过滤器技术细节
#### 1. web.xml配置

``` xml
<!--web.xml-->
<filter>
    <filter-name></filter-name>
    <filter-class></filter-class>
</filter>
<filter-mapping>
    <filter-name></filter-name>
    <url-pattern></url-pattern>
</filter-mapping>
```
#### 2. 过滤器执行流程
1. 执行过滤器，处理请求
2. 放行
3. 继续执行放行后的代码，主要处理响应

#### 3. 过滤器生命周期
1. init：服务器启动后，创建Filter对象，执行init()方法，仅执行一次。
2. doFilter：每一次请求拦截资源时，会执行
3. destroy：服务器关闭后，Filter对象被销毁，如果是正常关闭，会执行destroy方法。只执行一次。

#### 4. 过滤器配置详解
1. 拦截路径配置
    1. 具体资源路径：/index.jsp
    2. 目录拦截：/user/*
    3. 后缀名拦截：*.jsp
    4. 拦截所有资源：*
2. 拦截方式配置：资源被访问的方式过滤
设置dispatcherTypes属性：
- REQUEST：默认值，浏览器直接请求资源
- FORWARD：转发访问资源
- INCLUDE：包含访问资源
- ERROR：错误跳转资源
- ASYNC：异步访问资源

#### 5. 过滤器链
执行顺序，如果有两个过滤器，过滤器1和2
1. 过滤器1
2. 过滤器2
3. 资源执行
4. 过滤器2
5. 过滤器1

过滤器执行先后顺序
1. 注解配置：按照类名的字符串比较规则比较，值小的先执行
2. web.xml配置：<filter-mapping>谁定义在前面，谁先执行

## 七、Listener
概念：
事件监听机制，事件、事件源、监听器绑定在一起，事件源发生某个事件后，执行监听器代码。

快速实现：
1. 定义一个类，实现监听器接口
2. 复写方法
3. 配置监听器