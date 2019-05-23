---
title: JAVASE学习笔记—007 Java常用类库—日期时间类
date: 2019-05-23 13:56:31
tags:
- java
- javase
category:
- java
---

## 一、Date类

Date类表示特定的时间，精确到毫秒。

### 创建对象
1. Date()           用当前系统时间创建Date对象
2. Date(long date)  用毫秒时间戳创建Date对象

### 常用方法

1. after(Date when)             when是否在当前时间之后
2. befor(Date when)             when是否在当前时间之前
3. compareTo(Date anotherDate)  比较两个时间的先后
4. long getTime()               获取Date对象的时间戳
5. setTime(long time)           设置Date对象的时间戳
6. toString()

## 二、DateFormat类

1. 字符串转换为Date对象
   1. DateFormat.getInstance().parse(String)
2. Date对象转为字符串
   1. DateFormat.getInstance().format(Date)
3. 创建对象DateFormat(String format)
   1. format的形式 yyyy-MM-dd hh:mm:ss

子类：SimpleDateFormat

## 三、Calendar类
