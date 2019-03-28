---
title: Servlet+JDBC+Mysql实现用户登录功能示例
date: 2019-03-28 09:35:12
tags:
- java
category:
- java
---

## 一、用户登录需求
1. 登录页面，有用户名、密码两个输入框和登录按钮
2. 点击登录跳转
3. 登录成功跳转到成功页面，显示用户欢迎信息
4. 登录失败跳转到失败页面，显示错误信息

## 二、需求分析

1. login.html，
   1. username，password输入框
   2. 登录按钮
2. mysql建立user表
3. 使用Druid数据库连接池，jdbcTemplate操作数据库
4. Servlet
   1. 获取用户名和密码
   2. 验证登录
   3. 成功跳转到Success页面
   4. 失败跳转到error页面

<!-- more -->


## 三、代码实现
### 3.1 login.html
``` html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>登录</title>
</head>
<body>
<form method="post" action="/login.do">
    <table>
        <tr>
            <td>用户名：</td>
            <td><input type="text" name="username" required="required"/></td>
        </tr>
        <tr>
            <td>密码：</td>
            <td><input type="password" name="password" required="required"/></td>
        </tr>
        <tr>
            <td colspan="2"><input type="submit" value="登录"/></td>
        </tr>
    </table>
</form>
</body>
</html>
```

### 3.2 mysql建表

``` sql
CREATE TABLE tb_user(
	id INT PRIMARY KEY AUTO_INCREMENT,
	username VARCHAR(20) NOT NULL UNIQUE,
	PASSWORD VARCHAR(20) NOT NULL
);

INSERT INTO tb_user (username, PASSWORD) VALUES ('admin', '123');

SELECT username,PASSWORD FROM tb_user WHERE username='admin' AND PASSWORD='123';
```

### 3.3 java后端

#### druid.properties
```
driverClassName=com.mysql.jdbc.Driver
url=jdbc:mysql://localhost:3306/${db_name}
username=${username}
password=${password}
initialSize=1
maxActive=20
maxWait=6000
```

#### 数据库工具类，从连接池获取连接
``` java
package com.chan21252.login.util;

import com.alibaba.druid.pool.DruidDataSourceFactory;

import javax.sql.DataSource;
import java.io.InputStream;
import java.sql.Connection;
import java.sql.SQLException;
import java.util.Properties;

public class JDBCUtil {
    private static DataSource ds;

    /*
     * 初始化数据库连接池对象
     */
    static {
        try {
            Properties pro = new Properties();
            InputStream is = JDBCUtil.class.getClassLoader().getResourceAsStream("druid.properties");
            pro.load(is);
            ds = DruidDataSourceFactory.createDataSource(pro);
        } catch (Exception e) {
            e.printStackTrace();
        }
    }

    public static DataSource getDataSource() {
        return ds;
    }

    public static Connection getConnection() throws SQLException {
        return ds.getConnection();
    }

}
```

#### 封装用户信息
``` java
package com.chan21252.login.bean;

public class User {
    private int id;
    private String username;
    private String password;

    public int getId() {
        return id;
    }

    public void setId(int id) {
        this.id = id;
    }

    public String getUsername() {
        return username;
    }

    public void setUsername(String username) {
        this.username = username;
    }

    public String getPassword() {
        return password;
    }

    public void setPassword(String password) {
        this.password = password;
    }

    @Override
    public String toString() {
        return "User{" +
                "id=" + id +
                ", username='" + username + '\'' +
                ", password='" + password + '\'' +
                '}';
    }
}
```

#### user表操作类
``` java
package com.chan21252.login.dao;

import com.chan21252.login.bean.User;
import com.chan21252.login.util.JDBCUtil;
import org.springframework.jdbc.core.BeanPropertyRowMapper;
import org.springframework.jdbc.core.JdbcTemplate;

public class UserDao {

    private JdbcTemplate template = new JdbcTemplate(JDBCUtil.getDataSource());

    public User login(User loginUser) {
        try {
            String sql = "SELECT id,username,PASSWORD FROM tb_user WHERE username=? AND PASSWORD=?";

            User user = template.queryForObject(sql, new BeanPropertyRowMapper<>(User.class), loginUser.getUsername(), loginUser.getPassword());

            return user;
        } catch (Exception e) {
            e.printStackTrace();
            return null;
        }
    }
}
```

#### 登录servlet
``` java
package com.chan21252.login.web.servlet;

import com.chan21252.login.bean.User;
import com.chan21252.login.dao.UserDao;

import javax.servlet.ServletException;
import javax.servlet.annotation.WebServlet;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import java.io.IOException;

@WebServlet(
        name = "LoginServlet",
        urlPatterns = "/login.do"
)
public class LoginServlet extends HttpServlet {
    protected void doPost(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        request.setCharacterEncoding("utf-8");
        response.setCharacterEncoding("utf-8");

        String username = request.getParameter("username");
        String password = request.getParameter("password");

        User loginUser = new User();
        loginUser.setUsername(username);
        loginUser.setPassword(password);

        UserDao dao = new UserDao();
        User user = dao.login(loginUser);

        if (user != null) {
            request.setAttribute("msg", "欢迎您！" + user.getUsername());
            request.getRequestDispatcher("/success").forward(request, response);
        } else {
            request.setAttribute("msg", "用户名或密码错误");
            request.getRequestDispatcher("/error").forward(request, response);
        }
    }

    protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {

    }
}
```

#### 成功/失败页面
``` java
package com.chan21252.login.web.servlet;

import javax.servlet.ServletException;
import javax.servlet.annotation.WebServlet;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import java.io.IOException;
import java.io.PrintWriter;

@WebServlet(
        name = "SuccessServlet",
        urlPatterns = {"/success", "/error"}
)
public class SuccessServlet extends HttpServlet {
    protected void doPost(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        request.setCharacterEncoding("utf-8");
        response.setCharacterEncoding("utf-8");
        response.setContentType("text/html");
        PrintWriter out = response.getWriter();
        out.print(request.getAttribute("msg"));
    }

    protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        doGet(request, response);
    }
}

```

