---
title: JAVASE学习笔记—004 面向对象基础概述
date: 2019-05-07 09:55:38
tags:
- java
- javase
category:
- java
---

> 面向对象程序设计（英语：Object-oriented programming，缩写：OOP）是种具有对象概念的程序编程典范，同时也是一种程序开发的抽象方针。它可能包含数据、属性、代码与方法。对象则指的是类的实例。它将对象作为程序的基本单元，将程序和数据封装其中，以提高软件的重用性、灵活性和扩展性，对象里的程序可以访问及经常修改对象相关连的数据。在面向对象程序编程里，计算机程序会被设计成彼此相关的对象。

## 一、类的定义

Java允许开发者自定义数据类型（定义类），属于引用数据类型。

``` java
/**
 * 自定义一个表示人类的数据类型，属于java引用类型
 */
public class Person {

}
```

public：访问控制符
class：类关键字
Person：标识符，自定义类名

## 二、创建类的对象

一个Java类可以产生N个属于此类型的对象（示例）。类是制造对象的模板；对象是类的具体表现形式。

一个普通的Java类通常使用**new**运算符创建此类型对象。

``` java
Person perObject;
perObject = new Person();
```

## 三、对象在JVM中的存储
Person perObject = new Person();
Person perObject2 = perObject;

perOject变量存储在栈（Stack）中，保存的是Person对象的引用地址
new Person()创建了一个Person对象，对象数据存储在堆（Heap）中。

perObject赋值给perObject2变量，赋值的是对象的引用地址，perObject2中存储的也是同一个对象的地址。

![]()

### 四、类的成员实例属性（变量）定义和访问

#### 实例成员属性的声明
``` java
public class Person {
    String name;    //姓名
    int age;        //年龄
    String address; //地址
    char sex;       //性别
    double height;  //身高
}
```

实例属性属于对象所有，生命周期和所属对象的生命周期相同。不同对象的属性值是不相同的，不同对象的属性不能共享。


#### 实例属性的访问

实例属性属于对象，访问（赋值/取值）必须通过对象完成。

``` java
public class TestPerson {
    public static void main(String[] args) {
        Person p1 = new Person();

        p1.name = "任盈盈";
        p1.address = "黑木崖";
        p1.age = 20;
        p1.height = 165.50;
        p1.sex = '女';

        System.out.println(p1.name);
        System.out.println(p1.address);
        System.out.println(p1.age);
        System.out.println(p1.height);
        System.out.println(p1.sex);
    }
}
```

#### 访问修饰符

访问修饰符表明此属性可以在哪些范围内可以进行访问。

