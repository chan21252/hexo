---
title: JAVASE学习笔记—009 异常处理
date: 2019-05-29 10:12:03
tags:
- java
- javase
category:
- java
---

## 一、Java异常体系

1. Throwable
    1. Error
    2. Exception
        1. RuntimeException
        2. 非RuntimeException

Java异常分为运行时异常和编译型异常。
运行时异常只有在程序运行时被发现和处理。
编译型异常在编译阶段就能被发现，并且必须处理。
通常自定义异常都是编译型异常。


## 二、声明和抛出异常

``` java
//方法中抛出的异常，只能是方法声明的异常类型
方法名() throws 异常类型1,异常类型2{
    throw 异常对象
}
```

## 三、捕获和处理异常
1. 调用声明了编译型异常的方法必须进行异常捕获和处理
2. 使用try...catch捕获处理异常
    1. finally语句块中是必须执行的代码
3. 或者调用方法声明中继续抛出异常，由上层调用者处理

``` java
try{
    //可能抛出异常的代码
}catch(IOException e){
    //处理IO异常
}catch(Exception e){    //多个catch嵌套，后面的异常类型范围更大
    //处理其他异常
}finally{
    //一定会执行的语句
}

//JDK1.7以上版本新特性
try{

}catch(异常类型1 | 异常类型2 e){    //异常1和异常2不能有继承关系

}
```

### 异常常用的方法
1. getMessage() 获取异常信息
2. printStack() 打印异常方法调用堆栈

## 四、自定义异常

1. 自定义异常必须直接/间接继承Exception类
2. 通常在Java预定义异常不能满足要求时，会自定义异常。