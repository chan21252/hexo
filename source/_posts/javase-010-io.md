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

2. Writer（字符输出流）
    1. OutputStreamWriter
        1. FileWriter
    2. BufferedWriter
    3. CharArrayWriter
    4. StringWriter


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

## 三、序列化

将一个数据以java对象的形式存储到文件中称为序列化，或者从文件中获取一个java对象的操作称为反序列化。操作的对象被称为序列化对象。

Java规定任何实现Serializable接口的类，被看作序列化类，此类的实例是序列化对象。

### 序列化文件流

1. ObjectOutputStream：序列化流，将java对象文件保存到文件
2. ObjectInputStream：反序列化流，从文件中读取java对象

### 对象序列化和反序列化

1. ObjectOutputStream.writeObject(Object obj);
2. ObjectInputStream.readObject();

## 四、示例代码

``` java
import java.io.*;
import java.net.URI;
import java.util.Date;
import java.util.List;

/**
 * 文件管理类
 */
public class FileManager {
    /**
     * 创建文件对象
     *
     * @param uri 文件uri
     * @return File对象
     */
    public static File createFileAction(URI uri) {
        if (uri != null)
            return new File(uri);

        return null;
    }

    /**
     * 创建文件夹和文件夹内的文件
     *
     * @param dir       文件夹路径
     * @param fileNames 在dir中创建的文件
     */
    public static void createDir(String dir, List<String> fileNames) {
        File dirFile = new File(dir);

        if (!dirFile.exists()) { //目录不存在
            boolean bool = dirFile.mkdirs(); //创建目录
            if (bool) {  //目录创建成功
                for (String fileName : fileNames) {
                    File f = new File(dirFile, fileName);   //创建File对象
                    try {
                        f.createNewFile();  //新建文件
                    } catch (IOException e) {
                        e.printStackTrace();
                    }
                }
            }
        } else {
            System.out.println("目录已存在");
        }
    }

    /**
     * 读取二进制文件
     *
     * @param fileName 被读取文件的路径
     */
    public static void readBitFile(String fileName) {
        /* 1. 建立要读取的文件对象 */
        File file = new File(fileName);

        /* 2. 基于文件对象建立输入流 */
        InputStream in = null;
        if (file.exists()) {
            System.out.println("文件的长度： " + Math.ceil(file.length() / 1024.0) + "KB");
            try {
                in = new FileInputStream(file);
                long count = 0;  //读取到的字节数
                byte[] bytes = new byte[4096];  //临时存储读取到的二进制数据

                /* 3. 读取文件输入流内容 */
                /*
                循环读取数据，返回-1读取到文件末尾
                每次读取到的数据放入bytes数组
                 */
                while ((count = in.read(bytes, 0, bytes.length)) != -1) {
                    String s = new String(bytes, 0, (int) count);
                    System.out.print(s);
                }
                System.out.println();
            } catch (IOException e) {
                e.printStackTrace();
            } finally {
                /* 4. 关闭输入流 */
                try {
                    if (in != null)
                        in.close();
                    System.out.println("输入流关闭成功");
                } catch (IOException e) {
                    e.printStackTrace();
                }
            }
        } else {
            System.err.println("文件不存在");
        }
    }

    /**
     * 字符输入流读取文本文件
     *
     * @param file 要读取的文件对象
     */
    public static void readCharFile(File file) {
        FileReader fileReader = null;
        if (file.exists()) {
            try {
                fileReader = new FileReader(file);  //基于文本文件建议的输入流
                char[] chs = new char[48];  //字符临时缓冲区
                int count = 0;
                while ((count = fileReader.read(chs, 0, chs.length)) != -1) {
                    String s = new String(chs, 0, count);
                    System.out.print(s);
                }
                System.out.println();
            } catch (IOException e) {
                e.printStackTrace();
            } finally {
                if (fileReader != null) {
                    try {
                        fileReader.close();
                        System.out.println("字节输入流关闭成功");
                    } catch (IOException e) {
                        e.printStackTrace();
                    }
                }
            }
        } else {
            System.out.println("文件不存在");
        }
    }

    /**
     * 使用文本缓冲流读取文本文件
     *
     * @param file 要读取的文件对象
     */
    public static void useBufferReader(File file) {
        FileReader read = null;     //基于文件的普通输入流
        BufferedReader br = null;   //基于某个Reader建立的字符缓冲流
        if (file.exists()) {
            try {
                read = new FileReader(file);
                br = new BufferedReader(read);

                char[] chs = new char[2048];
                int count = 0;

                while ((count = br.read(chs, 0, chs.length)) != -1) {
                    String s = new String(chs, 0, count);
                    System.out.print(s);
                    System.out.println();
                }
            } catch (IOException e) {
                e.printStackTrace();
            } finally {
                try {
                    if (br != null)
                        br.close();
                    System.out.println("字符缓冲流关闭成功");

                    if (read != null)
                        read.close();
                    System.out.println("字符输入流关闭成功");
                } catch (IOException e) {
                    e.printStackTrace();
                }
            }
        }
    }

    /**
     * 将字节数组的内容写入到二进制文件
     *
     * @param filePath 文件路径
     */
    public static void binaryOutputStream(String filePath) {
        String str = "start=C:\\Users\\Administrator\\Desktop\\files\\bg.jpg";
        OutputStream out = null;

        try {
            out = new FileOutputStream(filePath);
            byte[] bytes = str.getBytes();
            out.write(bytes);   //将字节数组数据写入到目标文件
        } catch (IOException e) {
            e.printStackTrace();
        } finally {
            try {
                if (out != null) out.close();
            } catch (IOException e) {
                e.printStackTrace();
            }
        }
    }

    /**
     * 使用字节缓冲流输出写入文件
     *
     * @param file 写入文件
     */
    public static void useBufferedOutputStream(File file) {
        OutputStream out = null;
        BufferedOutputStream bs = null;
        String str = "床前明月光\n疑是地上霜\n举头望明月\n低头思故乡";
        //String str = "start=C:\\Users\\Administrator\\Desktop\\files\\bg.jpg";
        if (file.exists()) {
            try {
                out = new FileOutputStream(file.getAbsoluteFile() + "/思乡.doc");
                bs = new BufferedOutputStream(out);
                byte[] bytes = str.getBytes();
                bs.write(bytes);
                bs.flush();
            } catch (IOException e) {
                e.printStackTrace();
            } finally {
                try {
                    if (bs != null) out.close();
                    if (out != null) out.close();
                } catch (IOException e) {
                    e.printStackTrace();
                }
            }
        }
    }

    /**
     * 使用BufferedWriter写入文件
     *
     * @param dir
     */
    public static void useBufferedWriter(File dir, String fileName, String content) {
        if (dir.exists()) {
            File file = new File(dir, fileName);
            Writer writer = null;
            BufferedWriter bw = null;
            try {
                writer = new FileWriter(file);
                bw = new BufferedWriter(writer);
                char[] chs = content.toCharArray();
                bw.write(chs);
            } catch (IOException e) {
                e.printStackTrace();
            } finally {
                try {
                    if (bw != null) bw.close();
                    if (writer != null) writer.close();
                } catch (IOException e) {
                    e.printStackTrace();
                }
            }
        } else {
            System.out.println("路径：" + dir + "不存在");
            System.out.println("创建目录成功？" + dir.mkdirs());
        }
    }

    /**
     * 复制target文件到dir目录
     *
     * @param target 要复制的文件
     * @param dir    复制到的目录
     */
    public static void copyFile(File target, File dir) {
        if (target.exists()) {
            if (!dir.exists()) {
                dir.mkdirs();   //如果不存在，创建目录
            }
            if (!dir.isDirectory()) {
                System.out.println("dir不是目录！");
                return;
            }
            InputStream in = null;  //输入流
            OutputStream out = null;    //输出流
            File copyFile = null; //文件副本
            try {
                in = new FileInputStream(target);

                /*
                避免文件名重复被覆盖，使用系统时间的时间戳作为文件名前缀
                 */
                copyFile = new File(dir, new Date().getTime() + "-" + target.getName());
                out = new FileOutputStream(copyFile);

                byte[] bytes = new byte[1024];
                int count = 0;
                while ((count = in.read(bytes, 0, bytes.length)) != -1) {
                    System.out.println("文件复制中...请稍后");
                    out.write(bytes, 0, count);
                }
                System.out.println("文件复制完成");
            } catch (IOException e) {
                e.printStackTrace();
            } finally {
                try {
                    if (in != null) in.close();
                    if (out != null) in.close();
                } catch (IOException e) {
                    e.printStackTrace();
                }
            }
        } else {
            System.out.println("target文件不存在！");
        }
    }

    /**
     * 将Employee对象保存到文件（序列化）
     *
     * @param employee 序列化对象
     */
    public static void javaSerializable(Employee employee, File target) {
        if (employee == null)
            return;

        /*if (!target.exists()){
            try {
                boolean bool = target.createNewFile();
                if (!bool)
                    return;
            } catch (IOException e) {
                e.printStackTrace();
            }
        }*/

        OutputStream out = null;
        ObjectOutputStream oos = null;

        try {
            out = new FileOutputStream(target);
            oos = new ObjectOutputStream(out);
            oos.writeObject(employee);  //将序列化对象写入到文件中
        } catch (IOException e) {
            e.printStackTrace();
        } finally {
            try {
                if (oos != null) oos.close();
                if (out != null) out.close();
            } catch (IOException e) {
                e.printStackTrace();
            }
        }
    }

    /**
     * java反序列化文件
     *
     * @param target 目标文件
     */
    public static void javaDeserializable(File target) {
        if (!target.exists())
            return;

        InputStream in = null;
        ObjectInputStream ois = null;

        try {
            in = new FileInputStream(target);
            ois = new ObjectInputStream(in);
            //反序列化
            Object obj = ois.readObject();
            Employee employee = obj != null ? (Employee) obj : null;
            System.out.println(employee);
        } catch (IOException | ClassNotFoundException e) {
            e.printStackTrace();
        }
    }
}
```