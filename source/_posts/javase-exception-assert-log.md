---
title: java核心技术卷1读书笔记——异常、断言和日志
date: 2019-04-21 20:07:29
tags:
- java
category:
- java
---

## 一、异常
程序的运行状态不会一直处于理想状态，比如用户输入错误、要打开的文件找不到等等，程序本身也有可能存在BUG。这些都有可能引起程序崩溃或用户数据丢失，这让用户非常沮丧。避免这样的情况产生，程序员至少要做到以下三点：
1. 告知用户错误
2. 保存已有的数据结果
3. 告诉用户出现错误该如何操作或退出程序

java采用了**异常处理**的错误捕获机制。

### 1.1 java的异常继承体系
- Throwable
    - Error
    - Exception
        - RuntimeException
            - 子类
        - 非继承RuntimeException的异常
            - 子类

1. Erorr：指的是系统层级的错误，java程序是无能为力的，程序无法抛出/处理这样的错误。只能告知用户错误，安全退出。
2. Exception：程序异常，是编写java代码时候应该关心的。
    1. RuntimeException：程序自身产生的异常（如果出现RuntimeException，一定是程序员的问题）。比如错误的类型转换，数组访问越界。
    2. 非继承RuntimeException的异常：这种异常是否产生和运行环境有关，比如打开不存在的文件。

java规定，Error类、RuntimeException类以及它们的子类是不需要检查的异常，其他的异常是需要检查的异常（受查异常）。编译器为所有需要检查的异常都提供了异常处理器。

<!-- more -->

### 1.2 抛出异常
#### 声明抛出异常
java方法必须声明所有可能抛出的**受查异常**，非受查异常要么不可控（Error），要么应该避免发生（RuntimeException在程序员的掌控中）。

子类重写了父类的方法，子类方法只能和父类方法抛出相同异常或者更特定的异常（父类方法异常的子类）。

``` java
publiuc void read(String fileName) throws IOException {
    InputStream in = new FileInputStream(fileName);
    int b;
    while ((b = in.read()) != -1){
        //process input
    }
}
```

#### 主动选择时机抛出异常
1. 找到一个合适的异常类
2. 创建该异常类的对象
3. 将对象抛出

``` java
public void printSqrt(double a){
    if (a < 0){
        IllegalArgumentException e = new IllegalArgumentException("a不能小于0");
        System.out.println(e.getMessage());
        throw e;
    }
    System.out.println(Math.sqrt(a));
}
```

#### 自定义异常
自定义一个类，继承Exception或者Exception的子类。
所有异常类都有一个默认构造方法和一个带详细描述信息的构造方法。getMessage()方法可以获取描述信息。

### 1.3 捕获异常
如果异常被抛出，没有进行捕获，那么程序会终止执行。使用try/catch来捕获异常。

try语句块中的任何代码抛出了catch子句中的异常，那么：
1. 程序跳过try中剩余的代码
2. 执行对应catch子句的代码

如果调用了一个抛出受查异常的方法，要么捕获异常，要么继续传递异常（抛出）。捕获知道如何处理的异常，不知道如何处理的异常继续传递。

#### 捕获多个异常
可以存在多个catch子句处理多个异常。

> JavaSE7开始，如果多个异常的处理方法相同，并且无继承关系可以合并捕获。

#### catch再次抛出异常
可以在catch语句在再次抛出异常。使用场景对于方法调用者屏蔽了错误细节原因，只指出是否错误。
``` java
public void doRead(String fileName) throws Throwable {
    try {
        read(fileName);
    }catch (IOException e){
        //throw new MyException(e.getMessage());
        Throwable e1 = new MyException("读取文件错误");
        e1.initCause(e);
        throw e1;
    }
}
```

### 1.4 finally子句
finally子句跟在catch子句之后，不管是否有异常被捕获，finally子句的代码都被执行，常用来关闭资源。

``` java
try{
    //1
    //code might throw exception
    //2
}catch(Exception e){
    //3
    //process exception
    //4
}finally{
    //5
    //finally code
}//6
```

1. 如果try语句块没有抛出异常，执行1，2，5，6
2. try抛出了catch捕获的异常类型，并且catch没有抛出异常，执行1，3，4，5，6；如果catch抛出异常，执行1，3，5
3. try抛出了catch没有捕获的异常类型，执行1，5

finally和try中同时存在return，finally的return会覆盖try的结果。

> JavaSE7开始，实现了AutoCloseable接口的资源，放在try语句中，无论正常执行还是发生异常，都会自动调用close方法关闭资源，不需要在finally中关闭资源。

### 1.5 分析堆栈轨迹元素

//to-do




