---
title: JAVASE学习笔记—007 Java常用类库
date: 2019-05-19 21:29:15
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