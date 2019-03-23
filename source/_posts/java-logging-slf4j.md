---
title: slf4j——java日志门面
date: 2018-12-31 13:55:04
tags:
- java
categories:
- java
---

> 原文地址：[https://www.slf4j.org/manual.html](https://www.slf4j.org/manual.html)

SLF4J（Simple Logging Facade for Java）作为一个简单的门面或者抽象对象服务于各种日志框架，比如java.util.logging，logback，log4j。SLF4J允许终端用户在部署时插入想使用的日志框架。注意，在你的库/应用中启用SLF4J意味着只需添加一个叫做slf4j-api-*.jar的强依赖。

## Hello World
编程的传统惯例，这是一个例子，演示了使用SLF4J用最简单的方式输出“Hello World”。它开始于用名称“HelloWorld”获取一个logger对象。logger用来记录“Hello World”消息。

<!-- more -->

``` java
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;

public class HelloWorld {
  public static void main(String[] args) {
    Logger logger = LoggerFactory.getLogger(HelloWorld.class);
    logger.info("Hello World");
  }
}
```

为了运行这个例子，首先你需要[下载slf4j](https://www.slf4j.org/download.html)，然后解压它。解压完添加slf4j-api-*.jar文件到你的类路径中。

编译运行*HelloWorld*会在控制台中输出如下的结果：
>SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.

打印警告信息的原因是在你的类路径中找不到slf4j的绑定。

一旦你添加一个绑定到你的类路径中，警告就会消失。假设你添加了slf4j-simple-1.8.0-beta2.jar，你的类路径需要包含：
- slf4j-api-1.8.0-beta2.jar
- slf4j-simple-1.8.0-beta2.jar

现在编译运行*HelloWorld*会在控制输入如下结果：
> 0 [main] INFO HelloWorld - Hello World

## 经典使用模式
下面的示例代码演示了SLF4J的经典使用模式。注意，在第15行使用的占位符{}，更多细节参考FAQ["What is the fastest way of logging?"](https://www.slf4j.org/faq.html#logging_performance)。

``` java
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;

public class Wombat {
    final Logger logger = LoggerFactory.getLogger(Wombat.class);
    Integer t;
    Integer oldT;

    public void setTemperature(Integer temperature) {
        oldT = t;        
        t = temperature;

        logger.debug("Temperature set to {}. Old temperature was {}.", t, oldT);

        if(temperature.intValue() > 50) {
        logger.info("Temperature has risen above 50 degrees.");
        }
    }
}
```

## 在部署时与日志框架绑定
如之前提到的，SLF4J支持各种日志框架。SLF4J发行版附带了几个叫做“SLF4J bindings”的jar文件，每个文件对应一个支持的框架。

### slf4j-log4j12-1.8.0-beta2.jar
广泛使用的日志框架：[log4j1.2](http://logging.apache.org/log4j/1.2/index.html)版本的绑定。你还需要把log4j.jar放到你的类路径中。

### slf4j-jdk14-1.8.0-beta2.jar
JDK1.4中java.util.logging的绑定

### slf4j-nop-1.8.0-beta2.jar
[NOP](https://www.slf4j.org/api/org/slf4j/helpers/NOPLogger.html)的绑定，默默丢弃所有的日志。

### slf4j-simple-1.8.0-beta2.jar
简单日志实现的绑定，这会输出所有的事件到System.err中。只有INFO或者更高级别的消息会被打印。这种绑定往往在简单的应用中很有用。

### slf4j-jcl-1.8.0-beta2.jar
[Jakarta Commons Logging](http://commons.apache.org/proper/commons-logging/)的绑定。此绑定会委派所有SLF4J日志记录到JCL。

### logback-classic-1.0.13.jar (requires logback-core-1.0.13.jar)
**原生实现**SLF4J项目外还会有SLF4J的绑定,例如，原生实现SLF4J的[logback](https://logback.qos.ch/)。Logback的*ch.qos.logback.classic.Logger*类直接实现了SLF4J的*org.slf4j.Logger*接口。从而，将SLF4J和logback结合使用会严格限制0内存和计算开销。

要切换日志框架，替换类路径中的slf4j绑定就可以了。举个例子，要切换java.util.logging到log4j，用slf4j-log4j12-1.8.0-beta2.jar替换slf4j-jdk14-1.8.0-beta2.jar。

SLF4J不依赖于任何特殊的类加载器。实际上，每个SLF4J绑定在编译时都是硬连线的，使用有且只有一个特定的日志框架。举个例子，slf4j-log4j12-1.8.0-beta2.jar在编译时被绑定使用log4j。在你的代码中，除了slf4j-api-1.8.0-beta2.jar，你只需要将**有且只有一个**你选择的绑定放到合适的类路径中。不要在类路径中放超过一个绑定。这有一个图解说明。

![](http://image.5460cc.com/image/blog/java-logging-slf4j/001.png)

SLF4J的接口以及接口的各个适配器是非常简单的。大多数熟悉java语言的开发者应该有能力在一个小时内阅读和完全理解它的代码。不需要类加载器的知识，因为SLF4J不需要也无法直接访问任何类加载器。因此，SLF4J不会遇到Jakarta Commons Logging（JCL）出现的没有类加载器和内存泄露的问题。

鉴于SLF4J接口以及其部署模型的简单性，新的日志框架开发人员应该会非常容易编写SLF4J绑定。