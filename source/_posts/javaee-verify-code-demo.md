---
title: Java实现图片验证码功能
date: 2019-03-28 15:16:35
tags:
- java
category:
- java
---

## 一、验证码功能需求
1. 页面显示验证码图片，用户可以输入框输入验证码
2. 输入完毕后，点击验证
3. 验证成功，提示验证通过，失败提示验证不通过
4. 用户点击验证码图片刷新验证码

<!-- more -->

## 二、需求分析
1. 验证码用户页面
   1. 输入框
   2. 验证码图片
   3. 验证按钮
2. 随机生成验证码图片
   1. 生成验证码字符内容
   2. 绘制字符到图片
   3. 绘制干扰线
   4. 返回验证码字符内容和图片
3. 验证程序
   1. 验证用户输入和验证字符内容是否一致
   2. 返回验证信息

## 三、代码实现

验证码demo
``` java
package com.chan21252.verifycode.servlet;

//省略import

@WebServlet(
        name = "VerifyCodeDemoServlet",
        urlPatterns = "/verifyCodeDemo"
)
public class VerifyCodeDemoServlet extends HttpServlet {
    protected void doPost(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {

    }

    protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        int width = 100;
        int height = 50;

        //画布
        BufferedImage image = new BufferedImage(width, height, BufferedImage.TYPE_INT_RGB);

        //填充背景色
        Graphics g = image.getGraphics();   //画笔对象
        g.setColor(Color.PINK);     //设置背景色
        g.fillRect(0, 0, width, height);    //填充

        //绘制边框
        g.setColor(Color.BLUE);
        g.drawRect(0, 0, width-1, height-1);

        //绘制验证码
        String str = "ABCDEFGHIJKMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz0123456789";      //验证码字符样本

        Random random = new Random();

        for(int i=0; i<4; i++){
            //随机生成字符，绘制验证码
            int index = random.nextInt(str.length());
            char ch = str.charAt(index);
            g.drawString(ch+"", width / 5 * i + 20, height / 2);
        }

        //绘制干扰线
        g.setColor(Color.GREEN);
        for (int i=0; i<6; i++){
            int x1 = random.nextInt(width);
            int y1 = random.nextInt(height);
            int x2 = random.nextInt(width);
            int y2 = random.nextInt(height);
            g.drawLine(x1, y1, x2, y2);
        }

        //输出图片
        ImageIO.write(image, "jpg", response.getOutputStream());
    }
}
```

图片验证码生成工具类
``` java
package com.chan21252.verifycode.servlet;

import javax.imageio.ImageIO;
import java.awt.*;
import java.awt.image.BufferedImage;
import java.io.IOException;
import java.io.OutputStream;
import java.util.Random;

/**
 * 图形验证码工具类
 */
public class VerifyCodeUtils {
    private static int width;
    private static int height;
    private static Random random;
    private static String[] fontNames;
    private static String codes;
    private static Color bgColor;

    static {
        width = 100;
        height = 50;
        random = new Random();
        fontNames = new String[]{"宋体", "华文楷体", "黑体", "微软雅黑", "楷体_GB2312"};
        codes = "0123456789abcdefghjkmnopqrstuvwxyzABCDEFGHJKMNPQRSTUVWXYZ";
        bgColor = new Color(255, 255, 255);
    }

    private String text;

    //随机生成颜色
    private Color randomColor() {
        int r = random.nextInt(150);
        int g = random.nextInt(150);
        int b = random.nextInt(150);

        return new Color(r, g, b);
    }

    //随机生成字体
    private Font radomFont() {
        int index = random.nextInt(fontNames.length);
        String fontName = fontNames[index];
        int style = random.nextInt(4);
        int size = random.nextInt(5) + 24;

        return new Font(fontName, style, size);
    }

    //画干扰线
    private void drawLine(BufferedImage image) {
        int num = 3;    //3条干扰线
        Graphics2D g2 = (Graphics2D) image.getGraphics();
        for (int i = 0; i < num; i++) {
            int x1 = random.nextInt(width);
            int y1 = random.nextInt(height);
            int x2 = random.nextInt(width);
            int y2 = random.nextInt(height);

            g2.setStroke(new BasicStroke(1.5F));
            g2.setColor(Color.BLUE);
            g2.drawLine(x1, y1, x2, y2);
        }
    }

    //随机生成一个字符
    private char randomChar() {
        int index = random.nextInt(codes.length());

        return codes.charAt(index);
    }

    //创建绘制图片
    private BufferedImage createImage() {
        BufferedImage image = new BufferedImage(width, height, BufferedImage.TYPE_INT_RGB);
        Graphics2D g2 = (Graphics2D) image.getGraphics();
        g2.setColor(bgColor);
        g2.fillRect(0, 0, width, height);

        return image;
    }

    //生成验证码，并绘制
    public BufferedImage generateCaptcha() {
        BufferedImage image = createImage();    //创建图片缓冲区
        Graphics2D g2 = (Graphics2D) image.getGraphics();    //得到绘制环境
        StringBuilder sb = new StringBuilder();     //装载验证码字符

        for (int i = 0; i < 4; i++) {
            String s = randomChar() + "";
            sb.append(s);
            float x = i * 1.0F * width / 4;
            g2.setColor(randomColor());
            g2.setFont(radomFont());
            g2.drawString(s, x, height - 5);
        }

        this.text = sb.toString();

        drawLine(image);

        return image;
    }

    //返回验证码文本
    public String getText() {
        return text;
    }

    //保存图片
    public void output(BufferedImage image, OutputStream out)
            throws IOException {
        ImageIO.write(image, "JPEG", out);
    }
}
```

生成验证码，返回图片和字符串
``` java
package com.chan21252.verifycode.servlet;

//省略import

@WebServlet(
        name = "VerifyCodeGenServlet",
        urlPatterns = "/verifyCode"
)
public class VerifyCodeGenServlet extends HttpServlet {
    protected void doPost(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {

    }

    protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        VerifyCodeUtils verifyCodeUtils = new VerifyCodeUtils();
        BufferedImage codeImage = verifyCodeUtils.generateCaptcha();
        String codeStr = verifyCodeUtils.getText();

        System.out.println("生成新的验证码：" + codeStr);

        HttpSession session = request.getSession();
        session.setAttribute("code", codeStr);
        verifyCodeUtils.output(codeImage, response.getOutputStream());
    }
}
```

验证码校验，重定向到输入页面
``` java
package com.chan21252.verifycode.servlet;

//省略import

@WebServlet(
        name = "SubmitServlet",
        urlPatterns = "/submit.do"
)
public class SubmitServlet extends HttpServlet {
    protected void doPost(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {

        System.out.println("开始验证...");

        request.setCharacterEncoding("utf-8");

        String userInputStr = request.getParameter("verifyText");
        String codeStr = (String) request.getSession().getAttribute("code");

        System.out.println("用户输入：" + userInputStr);
        System.out.println("正确的验证码：" + codeStr);

        String msg;
        if (userInputStr.equalsIgnoreCase(codeStr)) {
            msg = "验证通过";
        } else {
            msg = "验证失败";
        }

        System.out.println(msg);

        request.getSession().setAttribute("msg", msg);
        response.sendRedirect("/verify.jsp");
    }

    protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        doPost(request, response);
    }
}
``` 


验证码输入页面
``` jsp
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<head>
    <title>人机验证</title>
</head>
<body>
    <div id="msg">${pageContext.session.getAttribute("msg")}</div>
    <form action="/submit.do" method="post">
        <img src="/verifyCode" id="verify-img"/>
        <br/>
        <input type="text" name="verifyText" id="input-text"/>
        <br/>
        <input type="submit" value="验证" id="input-btn"/>
    </form>

    <script>
        document.getElementById("verify-img").onclick = function () {
            document.getElementById("verify-img").src = "/verifyCode?" + new Date().getTime();
        };
    </script>
</body>
</html>
```

## 四、源代码下载
下载地址：[https://github.com/chan21252/VerifyCodeDemo/](https://github.com/chan21252/VerifyCodeDemo/)