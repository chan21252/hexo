---
title: JAVASE学习笔记—007 Java常用类库—日期时间类、Math类
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

<!-- more -->

## 三、Calendar类

### 创建对象

1. public static Calender getInstance()
2. public static Calender getInstance(Locale aLocale)

### 常量字段
1. AM/PM
2. YEAR/MONTH（0开始）/DATE
3. HOUR（12h）/HOUR_OF_DAY（24小时）/MINUTE/SECOND/MILLISECOND

### 常用方法

1. set(int field, int value)        设置日历字段的值，多个重载
2. get(int field)                   获取指定字段的值
3. add(int field, int value)        增加日历字段的值
4. after/before(Object when)        比较时间在之前/后
5. compareTo(Calender anotherCal)   比较日历对象时间的先后
6. **Date getTime()**               返回日历对象时间的Date对象

### 四、Math类

Math类定义了数学运算的基本功能，Math类所有属性和方法都是静态的，因此Math类不需要创建实例。
Math类是final修饰的，也不能被继承。

### 常量字段

1. E    自然对数的底数
2. PI   圆周率

### 常用方法
1. abs                          求绝对值
2. cbrt                         求立方根
3. ceil(double num)             返回大于等于num最小的double值（向上取整）
4. floor(double num)            返回小于等于num最大的double值（向下取整）
5. max/min(double a, double b)  求较大/较小值
6. pow(double a, double b)      返回a的b次幂
7. random                       产生0.0-1.0的随机数