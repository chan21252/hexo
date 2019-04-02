---
title: JavaEE-jsp基础
date: 2019-04-02 09:44:57
tags:
- java
category:
- java
---

## 一、概念
Java Server Page，java服务器页面。可以理解为，一个页面其中既可以定义html标签又可以定义java代码。用于简化编写servlet。

## 二、原理

> jsp本质是一个servlet。

<!-- more -->

![]()

## 三、jsp脚本元素
1. <% code %\>:在JAVA SERVLET的类的_jspService()方法中放入所包含的语句。
2. <%! code %\>:声明标签，在JAVA SERVLET的类体中放入一个变量的定义。静态的数据成员也可以如此定义。
3. <%= code %\>:表达式标签，在JAVA SERVLET的类中放入待赋值的表达式，表达式注意不能以分号结尾。

## 四、jsp指令
### 4.1 作用

用于配置jsp页面，导入资源文件。

### 4.2 格式

``<%@ 指令名称 属性名1=属性值1 属性名2=属性值2 %>``

### 4.3 分类
1. page：配置jsp页面
   1. contentType：等同于``response.setContentType();``
   2. pageEncoding：字符编码
   3. language：默认java
   4. buffer：输出缓冲区大小
   5. import：导入jar包
   6. errorPage：指定当前页面发生异常后跳转的页面
   7. isErrorPage：标识当前页面是否是错误页面
2. include：页面包含，导入页面的资源文件
   1. ``<%@include file="top.jsp" %>``
3. taglib：导入资源
   1. ``<%@taglib uri="http://java.sun.com/jsp/jstl/core" prefix="c" %>``

## 五、jsp注释

``<%-- --%>``

## 六、jsp内置对象

jsp页面中不需要创建，可以直接使用的对象。

内置对象：
1. pageContext：PageContext，当前页面共享数据，获取其他8个内置对象
2. request：HttpRequest，一次请求访问的多个资源（转发）
3. session：HttpSession，一次会话的多个请求间共享数据
4. application：ServletContext，所有用户共享数据
5. response：HttpResponse，响应对象
6. page：Object，当前页面（Servlet）的对象，this
7. out：JspWriter，输出对象
8. configure：ServletConfig，配置对象
9. exception：Throwable，异常对象，仅errorPage可以使用

## 七、EL表达式
### 7.1 概念
Expression Language，表达式语言。简化jsp页面的java代码编写。

### 7.2 语法

``` java
${表达式}
```

jsp默认开启支持EL表达式。可以设置page指令isELIgnored="true"，忽略当前jsp页面的EL表达式。

### 7.3 作用
1. 进行运算
2. 获取值
    1. el表达式只能从域对象中获取指定键的值
    2. 语法：``${域名称.键名}``
    3. 域名称：
        1. pageScope
        2. requestScope
        3. sessionScope
        4. applicationScope
    4. ${键名}：省略域名，会从最小的域开始寻找，直到找到有该键对应的值。
    5. el表达式隐式对象

## 八、JSTL
### 8.1 概念
JavaServer Page Tag Library JSP标准标签库

### 8.2 作用
用于简化和替换jsp页面的java代码

### 8.3 使用步骤
1. 导入jstl相关jar包
2. 引入标签库：taglib指令
3. 使用标签

### 8.4 常用的JSTL标签
1. if
2. choose
3. foreach