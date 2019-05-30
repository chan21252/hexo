---
title: JAVASE学习笔记—010 IO操作
date: 2019-05-30 10:34:36
tags:
- java
- javase
category:
- java
---

## 一、 File类

File类是表示文件对象和目录对象的数据类型。文件路径分隔符使用'/'或'\\'
文件可以存储字节和字符。目录可以存储文件和目录。
文件可以读取和写入。目录可以建立，删除，获取包含的目录和文件。

### 创建对象
1. File(File dir, String child)：
2. File(String pathName):
3. File(String parent, String child);
4. File(URI uri)

<!-- more -->

### 常见方法
1. 可读
2. 可写
3. 创建文件
4. 删除
5. 是否存在
6. 获取绝对路径
7. 获取文件名
8. 获取文件父目录
7. 获取文件路径
9. 判断是否是目录
10. 判断是否是文件
11. 返回目录下的文件列表
12. 创建文件夹

## 二、文件流

在Java中，文件的输入和输出是通过流（Stream）来实现的。一个流，必有源端和目的端，它们可以是计算机内存的某些区域，也可以是磁盘文件，甚至可以是 Internet 上的某个 URL。对于流而言，我们不用关心数据是如何传输的，只需要向源端输入数据，向目的端输出数据。

流按照处理数据的单位，可以分为字节流和字符流。字节流的处理单位是字节，通常用来处理二进制文件，例如音乐、图片文件等。而字符流的处理单位是字符，因为Java采用Unicode编码，Java字符流处理的即为Unicode字符，所以在操作汉字、国际化等方面，字符流具有优势。

### 文件流分类

1. 方向
    1. 输入流
    2. 输出流
2. 内容
    1. 字节流（byte）——二进制文件
    2. 字符流（char）——文本文件
3. 处理方式
    1. 节点流——针对文件建立
    2. 处理流——针对其他文件流建立

### 输入流

1. 输入流是建立在文件或其他文件流上对目标文件进行读取的文件流
2. 输入流只能读取，读取二进制文件使用字节流，读取文本文件使用字符输入流

#### Java输入流体系
1. InputStream（字节输入流的抽象类）
    1. FileInputStream
        1. BufferedInputStream
        2. DataInputStream
    2. ByteArrayInputStream
    3. ObjectInputStream

1. Reader（用于读取字符流的抽象类）
    1. InputStreamReader
        1. FileReader
    2. BufferedReader
    3. StringReader
    4. CharArrayReader

#### 字节输入流

主要方法
1. 读取
    1. int read() 读取下一个数据字节，返回读入缓冲区的字节总数，如果因为已经到达流末尾而没有更多的数据，则返回 -1。
    2. int read(byte[] b)   将 byte.length 个字节的数据读入一个 byte 数组中。
    3. int read(byte[] b, int offset, int len) 将 len 个字节的数据读入一个 byte 数组中。
2. 关闭

### 输出流

1. 输出流是指建立文件或其他文件流上来对目标文件进行写入
2. 输出流只对目标文件进行写入，写入二进制文件使用字节输出流，写入文本文件使用文本输出流。


#### Java输出流体系

1. OutputStream（字节输出流的抽象类）
    1. FileOutputStream
        1. BufferedOutputStream
        2. DataOutputtStream
    2. ByteArrayOutputStream
    3. ObjectOutputStream


### 字节输出流

1. 写入
2. 关闭

#### BufferedOutputStream.write()源码解析

``` java
OutputStream out = new FileOutputStream(file.getAbsoluteFile() + "/思乡.doc");
BufferedOutputStream bs = new BufferedOutputStream(out);;
byte[] bytes = str.getBytes();
bs.write(bytes, 0, bytes.length);   //并没有将数据写入到文件中去
```

查看相关方法源码：
``` java
/**
* @param      b     the data.
* @param      off   the start offset in the data.
* @param      len   the number of bytes to write.
* @exception  IOException  if an I/O error occurs.
*/
public synchronized void write(byte b[], int off, int len) throws IOException {
    if (len >= buf.length) {
        /* If the request length exceeds the size of the output buffer,
            flush the output buffer and then write the data directly.
            In this way buffered streams will cascade harmlessly. */
        flushBuffer();
        out.write(b, off, len);
        return;
    }
    if (len > buf.length - count) {
        flushBuffer();
    }
    System.arraycopy(b, off, buf, count, len);
    count += len;
}

/** Flush the internal buffer */
private void flushBuffer() throws IOException {
    if (count > 0) {
        out.write(buf, 0, count);
        count = 0;
    }
}

public BufferedOutputStream(OutputStream out) {
    this(out, 8192);    //缓冲区buf[]的长度默认是8192
}
```

分析：
1. 要写入的数据长度 >= 缓冲区的长度（写入的数据比缓冲区大）
    1. 刷新缓冲区：写入缓冲区已有的内容，重置缓冲区长度
    2. 将要写入的数据直接用OutputStream直接write（不缓冲）
2. 要写入的数据长度 > 缓冲区的长度-缓冲区已经缓冲的长度（写入的数据比缓冲区剩余的长度大）
    1. 刷新缓冲区：写入缓冲区已有的内容，重置缓冲区长度
    2. 将要写入的数据放入缓冲区，设置缓冲区已缓冲的长度
3. 写入的数据长度 <= 缓冲区的长度-缓冲区已经缓冲的长度（缓冲区剩余的长度能放下写入的数据）
    1. 将要写入的数据放入缓冲区，设置缓冲区已缓冲的长度，**并没有写入**

所以，要写入的数据长度在缓冲区能容纳的情况下，会放入缓冲区，并不会写入，需要**flush()**