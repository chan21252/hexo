---
title: JAVASE学习笔记—001 环境配置和HelloWorld
date: 2019-04-28 19:13:45
tags:
- java
- javase
category:
- java
---

## 一、JDK下载安装
### 1.1 下载
Java是Sun公司推出的产品，Sun公司现已被Oracle公司收购。我们可以去Oracle的官网下载Java开发工具（JDK）。Oracle官网：[https://www.oracle.com/index.html](https://www.oracle.com/index.html)。接着选择Menu->Products->Java->JavaSE，在页面底部找到**Java for developer**进行下载。目前JDK7和8版本使用比较广泛，这里下载的是JDK8的Windows版本。

<!-- more -->

具体步骤如图：
![](http://image.5460cc.com/image/jpg/java/javase/001-environment-config/01.jpg)
![](http://image.5460cc.com/image/jpg/java/javase/001-environment-config/02.jpg)
![](http://image.5460cc.com/image/jpg/java/javase/001-environment-config/03.jpg)
![](http://image.5460cc.com/image/jpg/java/javase/001-environment-config/04.jpg)

### 1.2 安装

// to-do

## 二、配置Windows系统环境变量

### 2.1 环境变量的概念：

环境变量是在操作系统中一个具有特定名字的对象，它包含了一个或者多个应用程序所将使用到的信息。例如Windows和DOS操作系统中的path环境变量，当要求系统运行一个程序而没有告诉它程序所在的完整路径时，系统除了在当前目录下面寻找此程序外，还应到path中指定的路径去找。用户通过设置环境变量，来更好的运行进程。

### 2.2 配置环境变量

步骤：
1. 配置JAVA_HOME环境变量
2. 配置系统环境变量的path值
3. 测试Java环境安装是否成功

本案例是win10系统。

### 2.3 打开环境变量
打开Windows的【系统】页面，选择【高级系统设置】，在弹出菜单中选择【环境变量】。
![](http://image.5460cc.com/image/jpg/java/javase/001-environment-config/05.jpg)

#### 2.4 新建JAVA_HOME环境变量
配置环境变量，如果给当前账户使用，在用户变量下选择【新建】；如果给所有用户使用，在系统变量下【新建】。
变量名输入：JAVA\_HOME，变量值输入JDK的安装目录，如D:\Program Files\Java\jdk1.8.0\_162
![](http://image.5460cc.com/image/jpg/java/javase/001-environment-config/06.jpg)

#### 2.5 设置path环境变量
在系统变量中，选择【path】，将JDK目录下的bin\和jre\bin目录加入到path中。

**注意**：据本人测试，win7系统可以写【%JAVA\_HOME%\bin】和【%JAVA\_HOME%\jre\bin】,win10这样写在cmd下执行javac命令会提示错误，因此win10就没有用JAVA_HOME变量+路径的写法。

![](http://image.5460cc.com/image/jpg/java/javase/001-environment-config/07.jpg)

启动cmd，输入java -version，显示jdk版本。输入java和javac显示如下信息，则环境配置成功。
![](http://image.5460cc.com/image/jpg/java/javase/001-environment-config/08.jpg)
![](http://image.5460cc.com/image/jpg/java/javase/001-environment-config/09.jpg)

## 三、第一个java程序，HelloWorld

实现一个能够在控制台界面输出“Hello World!”的java程序。

步骤：
1. 编写源代码（HelloWorld.java）
2. 编译源代码，得到字节码文件（HelloWorld.class）
3. 执行字节码文件

### 编写源代码。

第一次建议使用记事本编写。

``` java
//HelloWorld.java

//所有的java文件都是有类组成的
public class HelloWorld{
    //java程序开始执行的地方
    public static void main(String[] args){
        //在控制台输出Hello World!
        System.out.println("Hello World!");
    }
}
```

### 编译源代码：

1. 启动cmd，进入源代码文件（HelloWorld.java）所在的文件夹，输入javac HelloWorld.java，进行编译，这里其实是使用的jdk/bin/目录下的javac.exe进行编译。
2. 成功没有提示信息，在当前目录下会生成一个字节码文件（HelloWorld.class），如果代码有错误会有提示信息。

### 执行程序：
1. 继续输入java HelloWorld
2. 运行成功会输出Hello World!

![](http://image.5460cc.com/image/jpg/java/javase/001-environment-config/10.jpg)

## 四、java程序的编写，编译和执行过程

![](http://image.5460cc.com/image/jpg/java/javase/001-environment-config/11.jpg)

涉及到的编程词语：
1. 源文件：程序员用文本编辑器编写的代码文件。
2. 字节码文件（类文件）：源文件由java编译器编译后生成的字节码文件，是可执行文件。
3. 编译：对源文件进行编译，生成字节码文件。
4. 解释：java解释器对字节码文件进行解释，字节码文件就可以执行。
4. main方法（主方法）:一个普通java程序执行的入口点。
5. 控制台输出语句（System.out.println("Hello World!");）系统向控制台打印引号内的内容。

## 五、作业
### 任务1

从Oracle官方网站下载JDK8版本并安装到操作系统中，设置系统环境变量java_home 及 将java目录下的bin目录路径添加到系统环境变量path值当中；
在cmd命令行窗口使用javac 和 java 测试JDK是否安装成功。

### 任务2

1. 编写经典HelloWorld Java控制台应用程序，在cmd命令提示符窗口使用javac 编译HelloWorld源文件，使用java 解释器执行HelloWorld字节码文件，调试运行此控制台程序，理解java程序的编写，编译及执行过程。
2. 模仿以上HelloWorld程序编写一个能够打印自己信息的java控制台程序要求输出如下信息:
我叫: XXX
我是男（女）生
我今年XX岁
我是XX地方的人
我爱学习Java

## 六、题外话——非技术的自我提升
### 程序员应具备的基本标准
1. 规范的代码书写
2. 严谨的编程态度

### 学习编程应以手动实践为主
1. 每天保证一定的代码实践
2. 行动力，我想做，必须做，马上做
3. 代码量随着技能的熟练性和技能点增加而逐渐增多
