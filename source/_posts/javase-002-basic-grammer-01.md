---
title: JAVASE学习笔记—002 Java语法基础—（1）变量、常量、标识符和关键字
date: 2019-05-01 19:53:40
tags:
- java
- javase
category:
- java
---

## 一、Java类的简单定义

``` java
public class Test{

}
```

单词的含义：
- public: 访问修饰符关键字，含义是所有人都能访问该类
- class：表示类的关键字
- Test：类的名字
- {}：大括号表示类体，括号内是类的具体内容

<!-- more -->

## 二、main方法的结构和意义

普通的Java应用程序总是从main方法开始执行的。

``` java
public class Test{
    public static void main(String[] agrs){

    }
}
```

单词的含义：
- public：访问修饰符关键字
- static：静态关键字，main方法必须是静态的
- void：方法返回类型关键字，表示main方法返回值类型是void，即没有返回值
- main：方法名，java的主方法名字一定是main
- ()：括号内填写方法接受的参数
- String[] args：main方法接受的参数类型和参数名称，
    - String：参数类型是String，String是jdk中定义的字符串类型
    - String[]：[]代表参数是一个数组，String[]代表参数是一个String类型的数组
    - args：参数名称，可以自定义，不过main方法的参数名通常写args

## 三、Java注释

``` java
 /**
  * 文档注释
  * 这是一个测试注释的类
  * 包含一个main方法，程序从这里开始执行
  */
public class TestAni {
    /**
     * 这是一个main方法，一切从这里开始
     */
    public static void main(String[] args) {
        System.out.println("你好 Java");  //单行注释，这是一个输出语句
        //System.out.println("这条语句将被忽略");

        /*
        这是多行注释，对以下代码进行说明
         */
        System.out.println("我喜欢学习Java编程");
        /*
        System.out.println("此行代码不能输出信息");
         */
    }
}
```

## 四、标识符和关键字

### 标识符
标识符是由程序员自定义的，在源程序中代表某种含义的符号。Java中的标识符必需符合Java语言的规则。

标识符的定义规则：
1. 标识符只能包含英文字母（大小写）和各国文字，下划线（\_），数字（0-9），美元符号（$）
2. 标识符不能以数字开头

Java中哪些属于标识符和建议的定义方式：
+ 类名：首字母大写，多个单词拼成，单词首字母大写，其他小写（帕斯卡命名法），举例，MyClassName
+ 变量名：首字母小写，多个单词拼成，单词首字母大写，其他小写（驼峰命名法），举例，myVariable
+ 方法名：首字母小写，多个单词拼成，单词首字母大写，其他小写（驼峰命名法），举例，myMethod
+ 常量：全部大写，多个单词拼成，用下划线连接，举例，MAX_NUM

### 关键字

关键字是某种语言中代表特殊语义的预定义符号，程序员不能自定义关键字。

**关键字和标识符绝对不能互相代替使用！**

常见的关键字：
public，class，void，static，return，continue，break，int，double，boolean，true...

### 标识符和关键字的结合使用

``` java
public class IdentifierAndKeyword {
    public static void main(String[] args) {
        int stuAge = 23;    //stuAge是一个变量，属于标识符，int是关键字，表示变量的类型是整型，可以存储整数
        final int MAX_NUM = 25;     //MAX_NUM是一个常量标识符，final是关键字，表示该变量是常量
        String $我是变量 = "常量";

        if (stuAge > 17){   //if是一个关键字，表示条件判断
            System.out.println("成年人");
        }
    }
}
```

## 五、变量

变量是源程序对内存数据进行操作的一种引用。变量名属于标识符，应符合标识符的命名规则。

### 变量的声明和赋值

- 变量的声明定义由数据类型和名称组成，int age;
- 使用“=”为变量赋值，由右侧向左侧计算，age = 23;
- 声明定义变量同时为变量赋初始值，int age = 28;

**变量需要先声明并赋值，才能使用。**

### 变量的作用域

变量只在声明的类/方法中有效，不能在声明以外的范围使用变量。

- 局部变量：方法之中声明的变量，被称为该方法的局部变量
- 全局变量：方法之外声明的变量，方法内仍然可以使用，被称为全局变量。

``` java
public class TestVariable {
    static int quantity = 520;  //这是一个全局变量，在整个类中有效

    public static void main(String[] args) {
        int num;    //声明了一个int类型的变量num
        num = 100;  //为变量赋值
        System.out.println("num的值是 " + num);

        num = 200;  //更改变量的值为200
        System.out.println("num赋新的值是 " + num);

        //错误语句 age = 25;   //变量未声明便使用，是错误的
        int age = 25;
        System.out.println(age);
        //quantity在main方法外定义，main方法内仍然能使用
        System.out.println("全局变量 quantity 的值是 " + quantity);

        testGlobalVariable();   //这是调用一个static的静态方法

        System.out.println("全局变量 quantity 的值是 " + quantity);
    }

    public static void testGlobalVariable(){
        quantity = 1000;
        System.out.println(quantity);
    }
}

```

## 常量

常量就是不能被修改的数据，常量在程序中是只读的。

常量的分类
- 普通常量（常数）
- 标识符常量（final int MAX_NUM）

``` java
public class TestConstant {
    public static void main(String[] args) {
        final int MAX_NUM = 1000;   //java中常量的定义，关键字final
        System.out.println("常量 MAX_NUM 的值是 " + MAX_NUM);

        //MAX_NUM = 200;      //错误，常量的值不能被修改
    }
}

```