---
title: java-commons-fileupload
date: 2019-01-22 15:46:26
tags:
- java
categories:
- java
---

> 原文地址：[http://commons.apache.org/proper/commons-fileupload/index.html](http://commons.apache.org/proper/commons-fileupload/index.html)

## 概览
Commons FileUpload 让添加健壮、高性能的文件上传功能到你的servlet和web应用程序中变得十分容易。

FileUpload解析符合RFC 1867（HTML中基于表单的文件上传）的请求。就是说，如果一个HTTP请求，是用POST方法提交的，并且内容类型为“multipart/form-data”，FileUpload就可以解析这个请求，并且让结果易于调用者使用。

从1.3版本开始，FileUpload处理RFC 2047编码的header值。
<!-- more -->
发送一个multipart/form-data请求到服务器最简单的方式是通过web表单，即：
``` html
<form method="POST" enctype="multipart/form-data" action="fup.cgi">
    File to upload: <input type="file" name="upfile"><br/>
    Notes about the file: <input type="text" name="note"><br/>
    <br/>
    <input type="submit" value="Press"> to upload the file!
</form>
```

## 使用FileUpload
FileUpload可以有许多用法，看你应用的需要。最简单的，你可以调用一个独立的方法解析servlet请求，然后处理适用于程序的对象列表。另一方面，你可能决定自定义FileUpload来完全控制各个对象的存储方式，比如，你决定将内容流传输到数据库。

在这里，我们将介绍FileUpload的基本原理，并说明一些简单的、最常见的使用模式。自定义FileUpload将在别处说明。

FileUpload依赖于Commons IO，因此确保在类路径中有[依赖页](http://commons.apache.org/proper/commons-fileupload/dependencies.html)提到的Commons IO版本。

## 如何工作
文件上传请求包含了一个根据[RFC 1867](http://www.ietf.org/rfc/rfc1867.txt)编码的有序item列表。FileUpload可以解析请求，并且向你的应用提供单文件上传item的列表。无论其底层如何，每个item都实现了**FileItem**接口。

本文描述了commons fileupload库的传统API。传统API是一个便捷的方法，但是为了追求更佳的性能，你会更喜欢[Stream API](http://commons.apache.org/proper/commons-fileupload/streaming.html)。

每个文件item拥有许多你的应用会涉及到的属性。举例，每个item有名称和内容类型，可以提供**InputStream**获取它的数据。另一方面，你可能需要分别处理item，取决于item是否是常规的表单字段——就是说，数据是来自普通的文本框或类似的HTML字段，或者是一个上传的文件。FileItem接口提供了判定方法，并且以最合适的方式获取数据。

FileUpload用**FileItemFactory**来创建新的文件item。这就是FileUpload最大的灵活性。工厂最终控制每个item的创建方式。FileUpload附带的工厂实现存储item数据到内存或磁盘中，这取决于item的大小（比如数据的字节）。但是，可以自定义此行为以适合您的应用程序。

## Servlets 和 Portlets
从1.1版本开始，FileUpload支持servlet和porlet环境中的文件上传请求。在两个环境中的用法几乎相同，因此本文档的其余部分仅涉及servlet环境。

如果要构建portlet应用程序，以下是在阅读本文档时应该注意两个区别：
- 在您看到对**ServletFileUpload**类的引用，请替换为**PortletFileUpload**类。
- 在您看到对**HttpServletRequest**类的引用，请替换为**ActionRequest**类。

## 解析请求
在处理上传的item之前，你需要解析请求。确保是请求是文件上传请求很简单，FileUpload提供了一个静态方法。

``` java
// Check that we have a file upload request
boolean isMultipart = ServletFileUpload.isMultipartContent(request);
```

现在我们准备将请求解析为item组成。

## 最简单的示例
最简单的使用方案如下：
- 相当小的上传item应该被保存在内存中
- 较大的item应该被写入到磁盘的临时文件中
- 不应允许非常大的文件上传请求
- 要保存在内存中的item文件的默认最大值、允许上传请求的最大值和临时文件的位置是合适的

这种处理请求的方案可能不是最简单的：

``` java
// Create a factory for disk-based file items
DiskFileItemFactory factory = new DiskFileItemFactory();

// Configure a repository (to ensure a secure temp location is used)
ServletContext servletContext = this.getServletConfig().getServletContext();
File repository = (File) servletContext.getAttribute("javax.servlet.context.tempdir");
factory.setRepository(repository);

// Create a new file upload handler
ServletFileUpload upload = new ServletFileUpload(factory);

// Parse the request
List<FileItem> items = upload.parseRequest(request);
```

这就是所有要做的，真的。

解析结果是一个文件item的**List**，它的每个元素实现了**FileItem**接口。接下来讨论处理item。

## 实践更多的控制
如果你的使用方案和上面提到最简单的例子相近，但是你需要一些更多的控制，你可以自定义上传处理程序或者文件item工厂。下面的例子展示了几个配置项：

``` java
// Create a factory for disk-based file items
DiskFileItemFactory factory = new DiskFileItemFactory();

// Set factory constraints
factory.setSizeThreshold(yourMaxMemorySize);
factory.setRepository(yourTempDirectory);

// Create a new file upload handler
ServletFileUpload upload = new ServletFileUpload(factory);

// Set overall request size constraint
upload.setSizeMax(yourMaxRequestSize);

// Parse the request
List<FileItem> items = upload.parseRequest(request);
```

当然，每种配置方法都独立于其他配置方法。但是如果你想一次性配置工厂，你可以用构造函数来完成，像这样：

``` java
// Create a factory for disk-based file items
DiskFileItemFactory factory = new DiskFileItemFactory(yourMaxMemorySize, yourTempDirectory);
```

如果你想更多地控制请求解析过程，比如存储item到其他位置——数据库中，你可以查看[自定义FileUpload](http://commons.apache.org/proper/commons-fileupload/customizing.html)。

## 处理要上传的item
一旦解析完成，你会得到一个待处理文件item的**List**。大多数情况下，你会想要从常规的表单字段中分别处理文件上传，因此，你可能像这样处理list：

``` java
// Process the uploaded items
Iterator<FileItem> iter = items.iterator();
while (iter.hasNext()) {
    FileItem item = iter.next();

    if (item.isFormField()) {
        processFormField(item);
    } else {
        processUploadedFile(item);
    }
}
```

对一个常规的表单字段，你很可能只对item的名称和String值感兴趣。如你所愿，处理这些非常简单：

``` java
// Process a regular form field
if (item.isFormField()) {
    String name = item.getFieldName();
    String value = item.getString();
    ...
}
```

对文件上传，在处理内容前，有几个不同的事情你可能需要了解线下。这有一个你会感兴趣的方法示例：

``` java
// Process a file upload
if (!item.isFormField()) {
    String fieldName = item.getFieldName();
    String fileName = item.getName();
    String contentType = item.getContentType();
    boolean isInMemory = item.isInMemory();
    long sizeInBytes = item.getSize();
    ...
}
```

对于上传的文件，您通常不希望通过内存访问它们，除非它们很小，或者除非您没有其他选择。相反，您需要将内容作为流处理，或将整个文件写入其最终位置。 FileUpload提供了完成这两者的简单方法。

``` java
// Process a file upload
if (writeToFile) {
    File uploadedFile = new File(...);
    item.write(uploadedFile);
} else {
    InputStream uploadedStream = item.getInputStream();
    ...
    uploadedStream.close();
}
```

注意，FileUpload的默认实现，**write()**会尝试重命名文件为指定目标，如果数据已经在临时文件中。实际上只有因为某些原因重命名失败或者数据已经在内存中，才会复制数据。

如果确实需要访问内存中的上传数据，只需调用get（）方法即可将数据作为字节数组获取。

``` java
// Process a file upload in memory
byte[] data = item.get();
```

## 资源清理
本节仅适用于使用**DiskFileItem**的情况。换句话说，如果您上传的文件在处理之前写入临时文件，则适用。

这些临时文件会自动删除，如果它们不再被使用（更确切地，**DiskFileItem**实例是垃圾回收，这是由**org.apache.commons.io.FileCleanerTracker**类悄悄完成的，它启动了一个收割线程）。

如果不再需要，则应停止此收割者线程。在servlet环境中，这是通过使用名为FileCleanerCleanup的特殊servlet上下文侦听器来完成的。为此，请在web.xml中添加如下部分：

``` xml
<web-app>
  ...
  <listener>
    <listener-class>
      org.apache.commons.fileupload.servlet.FileCleanerCleanup
    </listener-class>
  </listener>
  ...
</web-app>
```

### 创建 DiskFileItemFactory
FileCleanerCleanup提供了org.apache.commons.io.FileCleaningTracker的实例。创建org.apache.commons.fileupload.disk.DiskFileItemFactory时必须使用此实例。这应该通过调用如下方法来完成：
``` java
public static DiskFileItemFactory newDiskFileItemFactory(ServletContext context,
                                                         File repository) {
    FileCleaningTracker fileCleaningTracker
        = FileCleanerCleanup.getFileCleaningTracker(context);
    DiskFileItemFactory factory
        = new DiskFileItemFactory(DiskFileItemFactory.DEFAULT_SIZE_THRESHOLD,
                                  repository);
    factory.setFileCleaningTracker(fileCleaningTracker);
    return factory;
}
```

### 禁用清除临时文件
要禁用对临时文件的跟踪，可以将FileCleaningTracker设置为null。因此，将不再跟踪创建的文件。特别是，它们将不再自动删除。