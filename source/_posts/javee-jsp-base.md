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
   6. errorpage：指定当前页面发生异常后跳转的页面
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
变量名
1. pageContext：PageContext
2. request：HttpRequest
3. session：HttpSession
4. application：ServletContext
5. response：HttpResponse
6. page：Object
7. out：JspWriter
8. configure：ServletConfig
9. exception：Throwable


