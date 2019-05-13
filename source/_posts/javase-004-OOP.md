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

<!-- more -->

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

![](http://image.5460cc.com/image/jpg/java/javase/004-oop/01.jpg)

## 四、类的实例成员属性定义和访问

### 实例成员属性的定义
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


### 实例成员属性的访问

成员实例属性属于对象，访问（赋值/取值）必须通过对象完成。

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

## 五、访问修饰符

访问修饰符表明此属性可以在哪些范围内可以进行访问。

![](http://image.5460cc.com/image/jpg/java/javase/004-oop/02.png)


## 六、类的属性和常量属性

static修饰的属性是类属性，归类所有，类的所有对象共享static属性。

final修饰的属性是常量，必须显式赋值，任何时候都不能修改常量的值。常量也使用static修饰符。

访问静态（static修饰）属性，建议使用类名.属性名来访问。

### 类的静态属性在jvm中的存储

public static String feature = "person";

1. feature变量存储于栈中，保存的是字符串的地址，指向"person"字符串。
2. "person"字符串存储于堆的静态区中。
3. 静态属性随着类的加载被加载，优于实例成员属性。

## 七、构造方法

构造方法：
1. 构造方法名称和类名完全相同，没有返回类型。
2. new创建对象时，会调用构造方法初始化对象。
3. 如果类中没有显式定义构造方法，会调用一个默认无参的public构造方法。 

构造方法的分类：
1. 无参数的构造方法。
2. 带参数的构造方法，作用是初始化当前对象的成员属性。

### this关键字

this关键字表示当前对象。
当局部变量和成员实例属性重名时，可以使用this关键字来指向成员属性。

### 构造方法重载
根据具体业务情况使用不同的构造方法对对象进行初始化。

任何显式定义的构造方法都会覆盖系统默认的无参构造方法，如果想使用无参构造方法，必须重新显式定义。

重载方法：
1. 函数名相同
2. 参数列表不同：个数不同，类型不同
3. 函数名相同，参数列表相同，仅返回值类型不同不是重载！

## 八、set和get方法

类的成员属性往往被设置为private，get和set方法为成员属性提供了一种安全访问方式。

## 九、类的方法

方法是解决问题的手段，就是为完成某个功能而存在；方法必须先定义后使用。

### 类的成员实例方法

实例方法定义：
[访问修饰符 ] 返回类型 方法名称([参数列表 ]){
    功能代码...
    [return... ]
}

实例方法调用：
对象.方法名([实参列表 ]);

### 方法的参数

值参数：
如果参数是值类型，参数传递是将实参中的数据拷贝一个副本，传递给形参。在调用过程中改变形参的值，不会改变实参中存储的值。

引用类型参数：
如果参数是引用类型，参数传递是将实参中的地址引用传递给形参，实参和形参指向同一个对象，修改形参的对象的数据，实参的数据也会改变。

<<<<<<< HEAD
### 方法的返回值

如果方法的定义有返回值，则必须使用**return**关键字返回一个符合返回值类型的值。
如果方法中使用了多个return，则必须保证只有一个return被执行，就是说方法只能有一个返回值。
=======
### 静态方法

静态方法属于类所有，与对象无关， 通常不会因为对象不同而改变方法的功能算法，静态方法必须使用关键字static修饰。

注意：
1. 静态方法中只能访问静态属性和静态方法
2. 静态方法不能直接访问实例属性和调用实例方法
3. this关键字不能出现在静态方法中

### 方法重载

方法重载是面向对象语言多态的特性，同名方法完成类似的功能，方法算法有所不同。

特点：
1. 在同一个类中
2. 方法名称完全相同
3. 参数必须有所不同（参数个数不同或者类型不同（位置不同））


## 十、示例代码

[https://github.com/chan21252/javase/tree/master/src/unit6](https://github.com/chan21252/javase/tree/master/src/unit6)
>>>>>>> 20b78b945753093b997574fe27945d4366414712
