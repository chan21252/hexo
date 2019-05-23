---
title: JAVASE学习笔记—007 Java常用类库—String类
date: 2019-05-20 20:46:15
tags:
- java
- javase
category:
- java
---

## 一、String类概述

String类代表字符串。Java中所有的字符串字面值都是此类的实例。
字符串在JVM中是以常理方式存储，它们的值在创建后不能更改，只能被覆盖。

### 构造方法

1. 默认无参构造器
2. byte数组构造器
3. char数组构造器
4. char数组，指定开始索引和长度的构造器
5. 字符串构造器
6. StringBuffer构造器

## 二、String类常用方法

1. public char charAt(int index)
2. public int compareTo(String target)
3. public boolean equals(String target)
4. public boolean equalsIgnoreCase(String target)
5. public boolean endWith(String suffix)
6. public boolean startWith(String prefix)

## 三、StringBuilder

StringBuilder和StringBuffer是可以修改字符串内容的类。
StringBuffer是线程安全的，建议在多线程中使用。
StringBuilder是非线程安全的，建议在单线程应用中使用，执行效率更高。

### 构造方法
1. 默认无参构造器
2. 指定缓冲区大小构造器
3. String字符串构造器
4. CharSequence构造器

### 常用方法

1. append(Object obj)                       在末尾添加字符串
2. delete(int start, int end)               删除指定起止索引的字符串内容
3. deleteCharAt(int index)                  删除指定索引位置的字符
4. insert(int offset, Object obj)           在指定位置插入字符串
5. replace(int start, int end, String str)  用字符串替换指定起止索引的内容
6. reverse()                                内容反转
7. setLength(int newLength)                 设置字符串长度，长度减少后，超出长度的内容被丢弃
8. setChatAt(int index, int ch)             设置指定索引位置的字符
