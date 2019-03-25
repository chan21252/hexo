---
title: JDBC
date: 2019-03-23 18:48:09
tags:
- java
- mysql
category:
- java
---

## JDBC入门
### 概念

JDBC是SUN公司定义的一套操作所有关系型数据库的java接口，各个数据库厂商去实现接口，提供数据库驱动jar包。JDBC编程真正执行的代码是驱动jar包中的实现类。

### 快速入门

1. 导入驱动jar包
2. 注册驱动
3. 获取数据库连接对象Connection
4. 定义sql
5. 获取执行sql语句的对象 Statement
6. 执行sql，接受返回结果
7. 处理结果
8. 释放资源

驱动jar包下载地址：[https://dev.mysql.com/downloads/connector/j/5.1.html](https://dev.mysql.com/downloads/connector/j/5.1.html)

## JDBC对象详解
### DriverManager（驱动管理对象）
功能：
1. 注册驱动
    1. com.mysql.jdbc.Driver类中存在静态代码块
    2. 
2. 获取数据库连接
    `` static Connection getConnection(String url, String user, String password) ``


### Connection（数据库连接对象）
功能：
1. 获取执行sql语句的对象
   1. Statement createStatement()
   2. PreparedStatement prepareStatement(String sql)
2. 管理事务
   1. 开启事务：void setAutoCommit(boolean autoCommit)，设置参数为false则开启事务
   2. 提交事务：commit()
   3. 回滚事务：rollback()

### Statement（执行sql对象）

功能：
1. 执行sql语句
   1. boolean execute(String sql)：执行任意sql语句
   2. int executeUpdate(String sql)：执行DML，DDL
   3. ResultSet	executeQuery(String sql)：执行DQL

### ResultSet（结果集对象）

ResultSet 对象具有指向其当前数据行的光标。最初，光标被置于第一行之前。next 方法将光标移动到下一行；因为该方法在 ResultSet 对象没有下一行时返回 false，所以可以在 while 循环中使用它来迭代结果集。

1. next()：光标向下一行
2. getX(参数)：
   1. X：数据类型，int getInt()
   2. 参数：
      1. int：代表列的编号，从1开始
      2. String：列的字段名称

### PreparedStatement（执行sql对象）
功能：
1. 解决sql注入问题，使用？作为占位符预编译sql语句。
2. 效率更高

``` java
PreparedStatement pstmt = con.prepareStatement("UPDATE EMPLOYEES
                                     SET SALARY = ? WHERE ID = ?");
   pstmt.setBigDecimal(1, 153833.00)
   pstmt.setInt(2, 110592)
```

## JDBC控制事务
1.


## 数据库连接池
###概念


### 实现
1. 标准接口：
2. 别人已实现的连接池：
    1. C3P0
    2. Druid（阿里巴巴提供）


### C3P0

官方地址：[https://www.mchange.com/projects/c3p0/](https://www.mchange.com/projects/c3p0/)

#### 使用步骤
1. 导入jar包
- c3p0-oracle-thin-extras-0.9.5.2.jar
- mchange-commons-java-0.2.11.jar
- 数据库驱动包

2. 定义配置文件
文件名c3p0-config.xml，放在src目录下

3. 创建数据库连接池对象
`` DataSource ds = new ComboPooledDataSource(); ``

4. 获取连接
`` Connection conn = ds.getConnection(); ``

#### demo
c3p0-config.xml

``` xml
<c3p0-config>
    <default-config>
        <property name="driverClass">com.mysql.jdbc.Driver</property>
        <property name="jdbcUrl">jdbc:mysql://localhost:3306/数据库</property>
        <property name="user">用户名</property>
        <property name="password">密码 </property>

        <property name="automaticTestTable">con_test</property>
        <property name="checkoutTimeout">30000</property>
        <property name="idleConnectionTestPeriod">30</property>
        <property name="initialPoolSize">10</property>
        <property name="maxIdleTime">30</property>
        <property name="maxPoolSize">100</property>
        <property name="minPoolSize">10</property>
        <property name="maxStatements">200</property>

        <user-overrides user="test-user">
            <property name="maxPoolSize">10</property>
            <property name="minPoolSize">1</property>
            <property name="maxStatements">0</property>
        </user-overrides>

    </default-config>
    <!-- This app is massive! -->
    <named-config name="intergalactoApp">
        <property name="acquireIncrement">50</property>
        <property name="initialPoolSize">100</property>
        <property name="minPoolSize">50</property>
        <property name="maxPoolSize">1000</property>

        <!-- intergalactoApp adopts a different approach to configuring statement caching -->
        <property name="maxStatements">0</property>
        <property name="maxStatementsPerConnection">5</property>

        <!-- he's important, but there's only one of him -->
        <user-overrides user="master-of-the-universe">
            <property name="acquireIncrement">1</property>
            <property name="initialPoolSize">1</property>
            <property name="minPoolSize">1</property>
            <property name="maxPoolSize">5</property>
            <property name="maxStatementsPerConnection">50</property>
        </user-overrides>
    </named-config>
</c3p0-config>
```

### Druid
1. 导入jar包
2. 定义配置文件druid.properties
3. 加载配置文件
4. 获取连接池对象
    `` DruidDataSourceFactory.createDataSource(pro); ``
5. 获取连接对象

#### 实践
1. 定义工具类

``` java
import com.alibaba.druid.pool.DruidDataSourceFactory;

import javax.sql.DataSource;
import java.io.InputStream;
import java.sql.Connection;
import java.sql.ResultSet;
import java.sql.SQLException;
import java.sql.Statement;
import java.util.Properties;

/**
 * Druid连接池工具类
 */
public class JDBCUtils {
    private static DataSource ds;

    static {
        try {
            Properties pro = new Properties();
            InputStream is = DruidDemo.class.getClassLoader().getResourceAsStream("druid.properties");
            pro.load(is);
            ds = DruidDataSourceFactory.createDataSource(pro);
        } catch (Exception e) {
            e.printStackTrace();
        }
    }

    /**
     * 获取连接对象
     */
    public static Connection getConnection() throws SQLException {
        return ds.getConnection();
    }

    /**
     * DML，DDL释放资源
     */
    public static void close(Statement stmt, Connection conn) {
        close(null, stmt, conn);
    }

    /**
     * DQL释放资源
     */
    public static void close(ResultSet rs, Statement stmt, Connection conn) {
        if (rs != null) {
            try {
                rs.close();
            } catch (SQLException e) {
                e.printStackTrace();
            }
        }

        if (stmt != null) {
            try {
                stmt.close();
            } catch (SQLException e) {
                e.printStackTrace();
            }
        }

        if (conn != null) {
            try {
                conn.close();
            } catch (SQLException e) {
                e.printStackTrace();
            }
        }
    }

    /**
     * 获取连接池方法
     */
    public static DataSource getDataSource() {
        return ds;
    }
}
```

2. druid.properties

``` properties
driverClassName=com.mysql.jdbc.Driver
url=jdbc:mysql://localhost:3306/db1
username=root
password=root
initialSize=5
maxActive=10
maxWait=3000
```





