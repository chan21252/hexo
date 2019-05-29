---
title: JAVASE学习笔记—008 泛型
date: 2019-05-28 11:12:57
tags:
- java
- javase
category:
- java
---

## 一、Java泛型概述

泛型是JavaSE1.5版本添加的特性。

泛型，即“参数化类型”。顾名思义，就是将类型由原来的具体的类型参数化，类似于方法中的变量参数，此时类型也定义成参数形式（可以称之为类型形参），然后在使用/调用时传入具体的类型（类型实参）。

泛型在java中不是一种数据类型，是一种在编译时的特殊语法，它能够让jvm在执行时擦除泛型描述，还原成未应用泛型的语法规则。

泛型的语法：<T>

<!-- more -->

### 泛型的优点

1. 提高开发效率和数据类型安全
2. 在编译阶段确定数据类型是否符合要求，避免错误发生
3. 避免对象强制类型转换操作
4. 支持动态确定数据类型

## 二、泛型在集合中的使用

``` java
Map<String, Set<Employee>> map = new HashMap<String, Set<Employee>>();

//泛型没有继承关系
List<Integer> intList = new ArrayList<Integer>();
List<Number> numList = new ArrayList<Number>();
//numList = intList;    //错误，泛型没有继承关系

//通配符泛型
List<?> list = intList;     //动态确定类型
```


## 三、带泛型的类

``` java
public class Generic<T>{
    public String getClassName(T t){
        return t.getClass().getName();
    }
}

public class TestGenerClass{
    public static void main(String[] main){
        Generic<Integer> gClass = new Generic<Integer>();
        gClass.getClassName(new Integer(600));
    }
}
```

多用于对不同类型对象进行相同操作时，在程序运行时确定传入参数的类型。

### 四、带泛型的接口

``` java
/**
 * 泛型接口
 */
public interface GenericInterface<T>{
    void save(T t);
}

/**
 * 实现类未指定泛型参数类型
 */
public class ImpClass<T> implements ImpClass<T>{
    @override
    void save(T t){
        //to-do
    }
}

/**
 * 实现类指定泛型参数类型
 */
public class ImpClass implements ImpClass<String>{
    @override
    void save(String str){
        //to-do
    }
}
```