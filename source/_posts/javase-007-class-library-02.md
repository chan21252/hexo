---
title: JAVASE学习笔记—007 Java常用类库—String类
date: 2019-05-20 20:46:15
tags:
- java
- javase
category:
- java
---

## 一、Object类

Object类是所有类的父类

### 常用方法

#### toString
返回Object对象的字符串表示形式。此方法可以在任意类中进行重写。

#### equals
判断给定对象是否与当前对象相等。此方法可以在任意类中进行重写。

``` java
//目标对象和当前对象相同返回true
public boolean equals(Object obj){
    return (this == obj);
}
```

#### getClass

返回当前对象的运行时类(Class)对象。此方法不可以被重写。

``` java
public final native Class<?> getClass();
```

#### hashCode方法
返回对象int类型的哈希码值。此方法为提高哈希表性能而支持，子类重写通常没有意义。

<!-- more -->

## 二、基本数据类型包装类

Java的8种基本数据类型都对应有一个包装类。

### Integer类

#### 构造方法

``` java
public Integer(int num);
public Integer(String numStr);  //字符串只包含数字字符

Integer intObj = 5; //也可以直接使用int常量来创建Integer对象
```

#### 常量：
1. Integer.MAX_VALUE
2. Integer.MIN_VALUE
3. Integer.BYTES    //字节数=4
4. Integer.SIZE     //位数=32

#### 常用方法

1. compareTo，比较大小
2. intValue（shortValue，longValue，floatValue，doubleValue），返回Integer对象的int值，实现从对象转换为基本数据类型
3. static int parseInt(String s)，静态方法，将s转换为10进制的int值返回
    1. 重载方法：static int parseInt(String s, int radix)，radix表示s的进制
4. static Integer valueOf(int i)，返回一个值是i的Integer对象
    1. 重载：static Integer valueOf(String s)
    2. 重载：static Integer valueOf(String s, int radix)
5. static String toBinaryString(int i)，返回i的二进制形式的字符串
    1. Integer.toOctalString(int i);   //八进制
    2. Integer.toHexString(int i);     //十六进制

### Character类

#### 创建对象

1. public Character(char c)
2. public static Character valueOf(char c)

#### 实例方法
1. public char charValue()，返回此对象的char值
2. public int compareTo(Character get)，比较大小
3. public String toString()，返回此对象char值的字符串形式

#### 静态方法
1. Character.isLowerCase(char c)，判断是否是小写字母
2. 其他检查字符的方法，参考JDK API文档

## 三、System类

System表示当前运行的JVM。System提供了标准输入、输出对象和错误输出流对象，垃圾回收，系统信息等。
System不能被实例化，是只读的。

### 获取输入、输出流
1. System.out   标准输出流
2. System.in    标准输入流
3. System.err   错误输出流

### 其他方法
1. exit(int status) 退出系统
2. gc() 通知垃圾回收系统期望快速回收垃圾以释放内存空间
3. currentTimeMillis()  当前系统的毫秒时间戳
4. getProperties()  获取当前系统属性的map集合
5. getProperty(String key)  获取某个属性的属性值

