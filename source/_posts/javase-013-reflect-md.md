---
title: JAVASE学习笔记—013 反射
date: 2019-06-10 12:51:03
tags:
- java
- javase
category:
- java
---

## 一、反射概述


java的反射是通过jvm在运行时获取某个类的字节码Class对象，从而反向获取此类型的内部信息的一种机制。

反射通常属于非常规操作，存在一定的安全问题。

使用反射的框架，spring，hibernate等。

## 二、反射相关类

java.lang.Class 字节码类型
java.lang.reflect.Constructor 构造器类型
java.lang.reflect.Method 方法类型
java.lang.reflect.Field 属性类型
java.lang.reflect.Modifier 访问修饰符类型

### Class类

Class 类的实例表示正在运行的 Java 应用程序中的类和接口。枚举是一种类，注释是一种接口。每个数组属于被映射为 Class 对象的一个类，所有具有相同元素类型和维数的数组都共享该 Class 对象。基本的 Java 类型（boolean、byte、char、short、int、long、float 和 double）和关键字 void 也表示为 Class 对象。

Class 没有公共构造方法。Class 对象是在加载类时由 Java 虚拟机以及通过调用类加载器中的 defineClass 方法自动构造的。


### Constructor类

Constructor 提供关于类的单个构造方法的信息以及对它的访问权限。

Constructor 允许在将实参与带有底层构造方法的形参的 newInstance() 匹配时进行扩展转换，但是如果发生收缩转换，则抛出 IllegalArgumentException。

### Field类

Field 提供有关类或接口的单个字段的信息，以及对它的动态访问权限。反射的字段可能是一个类（静态）字段或实例字段。

Array 允许在执行 get 或 set 访问操作期间进行扩展转换，但如果将发生收缩转换，则抛出一个 IllegalArgumentException。

### Method类

Method 提供关于类或接口上单独某个方法（以及如何访问该方法）的信息。所反映的方法可能是类方法或实例方法（包括抽象方法）。

Method 允许在匹配要调用的实参与底层方法的形参时进行扩展转换；但如果要进行收缩转换，则会抛出 IllegalArgumentException。