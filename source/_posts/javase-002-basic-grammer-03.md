---
title: JAVASE学习笔记—002 Java语法基础—（3）运算符和表达式
date: 2019-05-03 19:42:35
tags:
- java
- javase
category:
- java
---

## 一、Java中的运算符
1. 赋值运算符
2. 算术运算符
3. 关系运算符
4. 逻辑运算符
4. 位运算符
5. 特殊运算符

<!-- more -->

### 赋值运算符

赋值运算符用来将一个数据赋值给一个变量。

常用的赋值运算符：
> =，+=，-=，/=，*=，%=

``` java
int num = 5;
/*
求和赋值，左侧变量一定要声明并初始化
num += 10; 等价于 num = num + 10;
先计算num+10得出结果，赋值给num
*/
num += 10;
```

运算方向：从右往左
int quantity = 5000;    //右边计算得出结果，赋值给等号左边

### 使用Scanner接收键盘输入值
（1）创建一个Scanner对象。
Scanner是JDK1.6版本开始支持的一个类，位于java.util包中。
要接收从键盘输入值，要给Scanner传入System.in参数

（2）调用Scanner对象的nextInt()方法，程序会进入等待，接收用户输入一个整数

``` java
import java.util.Scanner;

public class AssignOpr {
    public static void main(String[] args) {
        //创建一个扫描仪对象
        Scanner input = new Scanner(System.in);
        int n2;
        System.out.println("请输入一个整数");
        n2 = input.nextInt();

        int n3 = 100;
        n3 -= n2;
        System.out.println("n3-n2结果是 " + n3);
    }
}
```

### 算术运算符

- 双目运算符（两个操作数，运算方向从左往右）：+，-，*（乘），/（商），%（模，求余）
- 单目运算符（一个操作数）：++（自增1），--（自减1）