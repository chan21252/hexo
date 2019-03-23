---
title: Maven5分钟快速入门
date: 2019-01-09 15:01:32
tags:
- java
categories:
- java
---

## 简介
Apache Maven是一个软件项目管理和理解工具。基于项目对象模型（POM）的概念，Maven可以从中心信息来管理项目的构建、报告和文档。

## 下载
下载链接：[http://maven.apache.org/download.cgi](http://maven.apache.org/download.cgi)
可以选zip和tar.gz包下载。

<!-- more -->

## 安装

>**Maven**是一个java工具，系统必须先安装java。
Maven安装过程非常简单：解压压缩包，添加包含mvn命令的bin目录到环境变量**PATH**即可。

具体步骤：

1. 确保**JAVA_HOME**环境变量已经设置，并且正确指向了JDK的安装目录；
2. 在maven安装目录下解压压缩包**unzip apache-maven-3.6.0-bin.zip**；
3. 解压后得到**apache-maven-3.6.0**目录，将其中的**bin**目录添加到环境变量**PATH**中。
4. 在cmd/shell中输入**mvn -v**，看到maven版本信息，确认安装成功。

``` bash
# mvn -v输出
Apache Maven 3.6.0 (97c98ec64a1fdfee7767ce5ffb20918da4f719f3; 2018-10-25T02:41:47+08:00)
Maven home: D:\Program\apache-maven-3.6.0\bin\..
Java version: 1.8.0_161, vendor: Oracle Corporation, runtime: D:\Program Files\Java\jdk1.8.0_161\jre
Default locale: zh_CN, platform encoding: GBK
OS name: "windows 10", version: "10.0", arch: "amd64", family: "windows"
```

## 创建项目
在你准备放置项目的目录中运行shell，执行如下命令：

``` bash
mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=my-app -DarchetypeArtifactId=maven-archetype-quickstart -DarchetypeVersion=1.4 -DinteractiveMode=false -X
```

> 如果你是刚装完maven，第一次运行会花费一些时间，这是因为maven正在下载最新的jar包和其他文件到本地仓库中。你可能需要多执行几次命令，才会成功。这是因为在完成所有的下载前，远程服务器有可能超时。

你会发现命令创建了一个和artifactId名称相同的目录。进入该目录

``` bash
cd my-app
```

my-app的目录结构如下：
```
my-app
|-- pom.xml
`-- src
    |-- main
    |   `-- java
    |       `-- com
    |           `-- mycompany
    |               `-- app
    |                   `-- App.java
    `-- test
        `-- java
            `-- com
                `-- mycompany
                    `-- app
                        `-- AppTest.java
```

src/main/java包含了项目的源代码，src/test/java包含了测试代码，pom.xml文件是项目的项目对象模型POM。

标准的项目结构参考这个链接：[http://maven.apache.org/guides/introduction/introduction-to-the-standard-directory-layout.html](http://maven.apache.org/guides/introduction/introduction-to-the-standard-directory-layout.html)

### POM

Maven中的**pom.xml**文件是项目的核心配置。它是一个单一的配置文件，包含了以你想要的方式来构建项目所需要的大部分信息。POM是庞大的，其复杂性可能让人望而生畏，但是在有效地使用它之前，没必要了解所有的复杂结构。项目的POM是这样：

``` bash
# pom.xml
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
  <modelVersion>4.0.0</modelVersion>
 
  <groupId>com.mycompany.app</groupId>
  <artifactId>my-app</artifactId>
  <version>1.0-SNAPSHOT</version>
 
  <properties>
    <maven.compiler.source>1.7</maven.compiler.source>
    <maven.compiler.target>1.7</maven.compiler.target>
  </properties>
 
  <dependencies>
    <dependency>
      <groupId>junit</groupId>
      <artifactId>junit</artifactId>
      <version>4.12</version>
      <scope>test</scope>
    </dependency>
  </dependencies>
</project>
```

### 我刚刚做了些什么
您执行了Maven目标*archetype*：*generate*，并将各种参数传递给该目标。前缀*archetype*的参数是提供了目标的插件*plugin*。如果你熟悉[Ant](http://ant.apache.org/)，你会觉得这和任务很类似。*archetype:generate*目标创建了一个基于[maven-archetype-quickstart](http://maven.apache.org/archetypes/maven-archetype-quickstart/) archetype的项目。我想说的是，一个*plugin*是一系列具有共同目标的*goal*。比如*jboss-maven-plugin*的目标是处理各种jboss项目。

## 构建项目

``` bash
mvn package
```

命令行会打印出各种操作，并以如下结尾：
```
...
[INFO] ------------------------------------------------------------------------
[INFO] BUILD SUCCESSFUL
[INFO] ------------------------------------------------------------------------
[INFO] Total time: 2 seconds
[INFO] Finished at: Thu Jul 07 21:34:52 CEST 2011
[INFO] Final Memory: 3M/6M
[INFO] ------------------------------------------------------------------------
```

与执行的第一个命令（*archetype：generate*）不同，您可能会注意到第二个命令只是一个单词 - *package*。和*goal*不同，这是一个*phase*。*phase*是构建生命周期中的一个步骤，它们是一个有序的阶段队列。当给出一个阶段时，Maven将执行序列中的每个阶段，直到并包括一开始定义的阶段。例如，如果我们执行编译阶段，那么实际执行的阶段就是：
1. validate
2. generate-sources
3. process-sources
4. generate-resources
5. process-resources
6. compile

您可以使用以下命令测试新编译和打包的JAR：
```
java -cp target/my-app-1.0-SNAPSHOT.jar com.mycompany.app.App
```

这会打印：**Hello World！**

## 运行Maven工具
### Maven阶段

虽然不是一个全面的列表，但这些是最常见的默认生命周期阶段。
- validate：验证项目是否正确，并提供所有必要信息
- compile：编译项目的源代码
- test：使用合适的单元测试框架测试编译的源代码。这些测试不需要被打包或部署
- package：获取已编译的代码并将其打包为可分发的格式，例如JAR
- integration-test：如有必要，将程序包处理并部署到可以运行集成测试的环境中
- verify：运行所有检查以验证包是否有效并符合质量标准
- install：将软件包安装到本地存储库中，以便在本地用作其他项目的依赖项
- deploy：在集成或发布环境中完成，将最终包复制到远程存储库以与其他开发人员和项目共享

除了上面的默认列表之外，还有另外两个Maven生命周期。他们是：
- clean：清除先前构建创建的工件
- site：为该项目生成站点文档

阶段实际上被映射到底层的各个目标。每个阶段执行的特定目标取决于项目的包装类型。例如，如果项目类型是JAR，则包执行jar：jar，如果项目类型是war，则执行war：war。值得注意的一点是，阶段和目标可以按顺序执行。

``` bash
# 此命令将清理项目，复制依赖项并打包项目。
mvn clean dependency:copy-dependencies package
```

### 生成站点文档

``` bash
# 此阶段根据项目的pom信息生成一个站点。您可以查看target / site下生成的文档
mvn site
```

## 总结
我们希望这个快速入门可以引起你对Maven的兴趣。这是一个非常简短的快速入门指南，你已经做好了解刚刚所有操作更详细信息的准备。参考[Maven入门指南](http://maven.apache.org/guides/getting-started/index.html)













