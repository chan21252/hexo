---
title: log4j——java日志框架
date: 2018-12-31 18:27:25
tags:
- java
categories:
- java
---

> 原文地址：[http://logging.apache.org/log4j/1.2/manual.html](http://logging.apache.org/log4j/1.2/manual.html)

## 摘要
本文档描述了log4j的API，它的特性和设计原理。Log4j是一个基于众多作者成果的开源项目。它允许开发者控制以任意粒度输出日志语句。它在运行时可以使用外部配置文件进行完全配置。最棒的是，log4j有一个平滑的学习曲线。注意：从用户的反馈看，它也很容易上瘾。

## 引言
几乎所有的大型应用都包含了它自身的日志或者跟踪API。根据这条规则，在1996年初*E.U. SEMPER*项目决定编写自己的跟踪API。经过无数次的改进、几次改版和众多工作，这些API演变成了log4j,一个受欢迎的java日志包。这个软件包在[Apache软件许可证](http://logging.apache.org/log4j/1.2/license.html)下发行。Apache 软件许可证是一个经[open source](https://opensource.org/)认证完全成熟的开源许可证。最新的log4j版本，包含全部源代码、class文件和文档可以在[http://logging.apache.org/log4j/](http://logging.apache.org/log4j/)找到。顺便说一句，log4j已被移植到C，C ++，C＃，Perl，Python，Ruby和Eiffel语言。

<!-- more -->

在代码中插入日志语句是调试代码的一种低技术含量方法。这可能也是唯一的方法，因为调试器总会有不可用和不适用的时候。多线程应用和分布式应用通常就是这种情况。

经验表明，日志是开发周期中的一个重要组成部分。日志有几个优点。它提供了有关应用运行的精确上下文。日志预计一旦插入到代码，日志的输出生成就不需要人为干预。而且，日志输出可以保持在持久介质中以便日后研究。除了在开发周期使用以外，一个足够丰富的日志包可以被视为审计工具。

正如Brian W. Kernighan和Rob Pike在他们的优秀著作《编程实践》中所说：
> As personal choice, we tend not to use debuggers beyond getting a stack trace or the value of a variable or two. One reason is that it is easy to get lost in details of complicated data structures and control flow; we find stepping through a program less productive than thinking harder and adding output statements and self-checking code at critical places. Clicking over statements takes longer than scanning the output of judiciously-placed displays. It takes less
time to decide where to put print statements than to single-step to the critical section of code, even assuming we know where that is. More important, debugging statements stay with the program;debugging sessions are transient.

日志记录也有缺点，它会拖慢应用的速度。如果过于冗余，它会引发盲目地回滚。为了缓解这些问题，log4j被设计为可靠、快速和可扩展的。由于日志记录并不是应用程序的主要关注点，log4j API力求易于理解和使用。

## Loggers, Appenders和Layouts
Log4j有三个主要组件：logger，appenders和layouts。这三类组件协同工作，使开发者能根据消息类型和级别在运行时控制这些消息的输出格式和位置。

### Logger的层次结构
任何日志API优于*System.out.println*最明显也是最重要的地方在于，它们可以禁用某些日志语句，同时运行其他语句打印不受影响。假定了日志空间，即所有可能的日志语句的空间的功能可以根据开发者的某些选择条件进行分类。作为软件包的核心理念，它很有前瞻性地让我们选择分类。然而，从1.2版本以后，Logger类已经取代了Category类。对于熟悉log4j早期版本的人来说，Logger类可以被视为Category类的别名。

Logger是有名称的实体。Logger的名称区分大小写，遵循分层命名规则：

> 如果跟在一个“.”号后面的logger名称是后代logger名称的前缀，这个logger被称作另个一个logger的祖先。如果在logger和它的后代logger之间没有祖先，则称这个logger是子logger的父代。

举个例子，叫做“com.foo”的logger是“com.foo.Bar”logger的父亲。相似地，“java”是“java.util”和“java.util.Vector”的祖先。大多数开发者都很熟悉这种命名方案。

root logger是logger级别中的最高级，它有以下2点特征：
1. 总是存在
2. 无法通过名称检索

调用静态方法*Logger.getRootLogger*可以检索root logger。其他的logger可以通过*Logger.getLogger*方法实例化和检索。此方法将想要的logger作为参数。Logger的一些基础的方法如下：

``` java
package org.apache.log4j;

public class Logger {
    // Creation & retrieval methods:
    public static Logger getRootLogger();
    public static Logger getLogger(String name);

    // printing methods:
    public void trace(Object message);
    public void debug(Object message);
    public void info(Object message);
    public void warn(Object message);
    public void error(Object message);
    public void fatal(Object message);

    // generic printing method:
    public void log(Level l, Object message);
}
```

可以给logger分配等级，可用的等级有：
- TRACE
- DEBUG
- INFO
- WARN
- ERROR
- FATAL

这些都被定义在*org.apache.log4j.Level*类中。尽管我们不推荐您这么做，你可以通过定义*Level*的子类定义自己的等级。稍后将讲述一个更好的方法。

如果给定的logger没有分配级别，那么它将从具有指定级别的最近祖先继承一个级别。更正式的说：
> 一个给定的logger C的继承等级，等于logger层次结构中的第一个非空级别，从C开始并且向上追溯直到root logger。

为确保所有logger最终都能继承一个级别，root logger始终具有指定的级别。

下面是四个表，其中包含各种已分配的级别值以及根据上述规则生成的继承级别。

![](http://image.5460cc.com/image/blog/java-logging-log4j/001.png)

日志记录请求通过调用logger实例的一种打印方法来实现。打印方法有*debug, info, warn, error, fatal, log*。

根据定义，打印方法确定日志记录请求的级别。例如，如果c是logger实例，则语句*c.info（“..”）*是级别INFO的记录请求。

如果日志记录请求的级别高于或等于其logger的级别，则称其是已启用的。否则，该请求被禁用。没有指定级别的logger将从层次结构中继承一个级别。该规则总结如下：

> 在级别q的logger（被指定的或者继承的，以实际为准）中，进行级别p的记录请求，如果p>=q，则该记录请求被启用。

该规则是log4j的核心。log4j指定的基本是有序的。对标准的级别，排序是**DEBUG < INFO < WARN < ERROR < FATAL**。

这是一个该规则的示例。

``` java
// get a logger instance named "com.foo"
Logger  logger = Logger.getLogger("com.foo");

// Now set its level. Normally you do not need to set the
// level of a logger programmatically. This is usually done
// in configuration files.
logger.setLevel(Level.INFO);

Logger barlogger = Logger.getLogger("com.foo.Bar");

// This request is enabled, because WARN >= INFO.
logger.warn("Low fuel level.");

// This request is disabled, because DEBUG < INFO.
logger.debug("Starting search for nearest gas station.");

// The logger instance barlogger, named "com.foo.Bar",
// will inherit its level from the logger named
// "com.foo" Thus, the following request is enabled
// because INFO >= INFO.
barlogger.info("Located nearest gas station.");

// This request is disabled, because DEBUG < INFO.
barlogger.debug("Exiting gas station search");
```

调用相同名称的*getlogger*方法总会返回相同logger对象的引用。例如，

``` java
Logger x = Logger.getLogger("wombat");
Logger y = Logger.getLogger("wombat");
```
x,y指向了相同的logger对象。

因此，可以配置一个logger，然后在代码中检索相同的实例，而不用传递引用。和生物学的父子关系（父母总是先于孩子）不同，log4j可以以任意顺序创建和配置logger。特别地，父logger会查找并链接到其后代，即使父logger在后代logger之后被实例化。

log4j的环境配置通常在程序初始化时完成。更好的方法是读取一个配置文件。这个方法很快会介绍。

log4j使得通过程序组件命名logger非常简单。这可以通过在每个类中静态实例化一个logger完成，logger名称等于该类的完全限定名称。这是一个非常有效且直截了当的定义logger的方法。由于日志输出带有生成日志logger的名称，这种命名策略可以轻松识别日志消息的来源。但是，这只是一个可能的logger命名策略，尽管很常见。log4j不限制logger的名称，开发者可以根据需要自由命名。

尽管如此，遵循logger所在的类命名logger似乎是目前已知的最佳策略。

### Appenders and Layouts
基于logger，选择性地启用或禁用日志记录请求只是一部分。log4j允许日志记录请求打印到多个目标。在log4j中，输出目的地被称作*appender*。当前，存在控制台、文件、GUI组件、远程套接字服务器、JMS、NT Event Loggers和远程UNIX Syslog守护程序的appender。它也可以异步记录。

一个logger可以链接多个appender。

*addAppender*方法添加一个appender到给定的logger中。**一个给定logger中，每个启用的日志记录请求会被转发给它所有的appender以及更高层次结构logger的appender。**换句话说，appender可以从logger层级结构中额外被继承。比如，一个控制台appender被添加到root logger，所以所有启用的日志记录请求至少被打印到控制台。如果添加一个文件appender到一个叫做C的logger中，C和C的祖先logger中启用的日志记录请求会被打印到文件和控制台中。通过设置additivity标记为false，可以覆盖默认行为，从而appender不再是累积的。

有关appender可加性的规则总结如下：
> logger C的一条日志语句输出会到达C和C祖先的所有appender中。但是，如果一个logger C的祖先，叫做P，它的additivity标记设置为false，那么C的输出会被定向到C和C祖先（追溯到且包括P的）的所有appender中，但P的祖先不在其中。

下面的表格展示了一个示例：
![](http://image.5460cc.com/image/blog/java-logging-log4j/002.png)

通常，用户希望自定义的不仅仅是输出目的地，还有输出格式。自定义输出格式可以通过给appender关联layout实现。根据用户的期望，layout负责格式化日志记录请求，而appender负责发送格式化的输出到它的目的地。

*PatternLayout*，是标准log4j发行版本的一部分，它允许用户根据类似C语言*printf*函数的转换模式指定输出格式。

比如。转换模式为“%r [%t] %-5p %c - %m%n”的PatternLayout将进行类似于这样的输出：
> 176 [main] INFO  org.foo.Bar - Located nearest gas station.

第一个字段是自程序启动开始已经过去的毫秒数。第二个字段是发出日志请求的线程。第三个字段是日志语句的等级。第四个字段是和日志请求相关的logger。在“-”之后的文字是语句消息。

同样重要的是，log4j根据用户指定的标准渲染日志消息的内容。比如，如果你经常要记录日志*Orange*，你当前项目中的一个对象类型，你可以注册一个*OrangeRender*，它会在Orange需要被记录时被调用。

对象渲染遵循类层次结构。举个例子，假设橙子是水果，如果你注册一个*FruitRender*，所有水果包括橙子都会*FruitRender*被渲染，当然除非你给橙子指定了*OrangeRender*。

## 配置
在程序代码中插入日志请求需要相当多的规划和功夫。调查显示，大约4%的代码被专门用来记录日志。因此，即便是中等规模的程序都会在其代码中嵌入数以千计的日志记录语句。鉴于语句的数量，不用手动修改来管理日志记录语句就变得势在必行。

log4j环境可以通过编程完全配置，但是使用配置文件配置log4j就灵活的多。目前，配置文件可以用XML和Java Properties（键=值）方式配置。

让我们在一个使用log4j的假想应用程序MyApp的帮助下，看看配置是如何实现的。

``` java
import com.foo.Bar;

// Import log4j classes.
import org.apache.log4j.Logger;
import org.apache.log4j.BasicConfigurator;

public class MyApp {

    // Define a static logger variable so that it references the
    // Logger instance named "MyApp".
    static Logger logger = Logger.getLogger(MyApp.class);

    public static void main(String[] args) {

        // Set up a simple configuration that logs on the console.
        BasicConfigurator.configure();

        logger.info("Entering application.");
        Bar bar = new Bar();
        bar.doIt();
        logger.info("Exiting application.");
    }
}
```

*MyApp*首先导入log4j相关的类。然后它用*MyApp*类的完全限定名称定义了一个静态logger变量。

*MyApp*使用了定义在*com.foo*中的*Bar*类。

``` java
package com.foo;
import org.apache.log4j.Logger;

public class Bar {
    static Logger logger = Logger.getLogger(Bar.class);

    public void doIt() {
        logger.debug("Did it again!");
    }
 }
```
调用BasicConfigurator.configure方法创建了一个相当简单的log4j设置。这个方法将root logger硬连线到*ConsoleAppender*。使用*PatternLayout*“%-4r [%t] %-5p %c %x - %m%n”来格式化输出。

注意，root logger默认被指定为*Level DEBUG*。

MyApp的输出如下：

> 0    [main] INFO  MyApp  - Entering application.
36   [main] DEBUG com.foo.Bar  - Did it again!
51   [main] INFO  MyApp  - Exiting application.

下图描绘了在调用了*BasicConfigurator.configure*后，*MyApp*的对象图。

![](http://image.5460cc.com/image/blog/java-logging-log4j/003.png)



