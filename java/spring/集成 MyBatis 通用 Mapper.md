MyBatis 是一款优秀的持久层框架，它支持定制化 SQL、存储过程以及高级映射。MyBatis 避免了几乎所有的 JDBC 代码和手动设置参数以及获取结果集。MyBatis 可以使用简单的 XML 或注解来配置和映射原生信息，将接口和 Java 的 POJOs(Plain Old Java Objects,普通的 Java 对象)映射成数据库中的记录。

#### 数据表

![](https://upload-images.jianshu.io/upload_images/15141093-318dc192fc856090.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

#### ORM 模块结构

对象关系映射（Object Relational Mapping，简称ORM，或O/RM，或O/R mapping），是一种程序技术，用于实现面向对象编程语言里不同类型系统的数据之间的转换。从效果上说，它其实是创建了一个可在编程语言里使用的“虚拟对象数据库”。

![](https://upload-images.jianshu.io/upload_images/15141093-1dc583395a4531a8.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


#### pom.xml

```
<?xml version="1.0" encoding="UTF-8"?>

<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
  <modelVersion>4.0.0</modelVersion>

  <groupId>com.spring</groupId>
  <artifactId>orm</artifactId>
  <version>1.0-SNAPSHOT</version>

  <name>orm</name>
  <!-- FIXME change it to the project's website -->
  <url>http://www.example.com</url>

  <properties>
    <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
    <maven.compiler.source>1.7</maven.compiler.source>
    <maven.compiler.target>1.7</maven.compiler.target>

    <spring.version>5.1.5.RELEASE</spring.version>
    <aspectj.version>1.9.2</aspectj.version>
    <junit.version>4.12</junit.version>
    <log4j.version>1.2.17</log4j.version>
    <slf4j.version>1.7.12</slf4j.version>

    <mysql.version>5.1.47</mysql.version>
    <mybatis.version>3.5.0</mybatis.version>
    <mybatis-spring.version>2.0.0</mybatis-spring.version>
    <tk-mybatis.version>4.1.5</tk-mybatis.version>
    <druid.version>1.1.14</druid.version>


  </properties>

  <dependencies>
    <!--springContext依赖-->
    <dependency>
      <groupId>org.springframework</groupId>
      <artifactId>spring-context</artifactId>
      <version>${spring.version}</version>
    </dependency>
    <!--spring-tx事务依赖-->
    <dependency>
      <groupId>org.springframework</groupId>
      <artifactId>spring-tx</artifactId>
      <version>${spring.version}</version>
    </dependency>
    <!--spring-jdbc依赖-->
    <dependency>
      <groupId>org.springframework</groupId>
      <artifactId>spring-jdbc</artifactId>
      <version>${spring.version}</version>
    </dependency>
    <!--mysql依赖-->
    <dependency>
      <groupId>mysql</groupId>
      <artifactId>mysql-connector-java</artifactId>
      <version>${mysql.version}</version>
    </dependency>
    <!--mybatis依赖-->
    <dependency>
      <groupId>org.mybatis</groupId>
      <artifactId>mybatis</artifactId>
      <version>${mybatis.version}</version>
    </dependency>
    <!--mybatis-spring依赖-->
    <dependency>
      <groupId>org.mybatis</groupId>
      <artifactId>mybatis-spring</artifactId>
      <version>${mybatis-spring.version}</version>
    </dependency>
    <!--通用mapper依赖-->
    <dependency>
      <groupId>tk.mybatis</groupId>
      <artifactId>mapper</artifactId>
      <version>${tk-mybatis.version}</version>
    </dependency>
    <!--druid连接池依赖-->
    <dependency>
      <groupId>com.alibaba</groupId>
      <artifactId>druid</artifactId>
      <version>${druid.version}</version>
    </dependency>
    <!--Lombok依赖-->
    <dependency>
      <groupId>org.projectlombok</groupId>
      <artifactId>lombok</artifactId>
      <version>1.16.18</version>
    </dependency>
    <!--springAOP依赖-->
    <dependency>
      <groupId>org.springframework</groupId>
      <artifactId>spring-aop</artifactId>
      <version>${spring.version}</version>
    </dependency>
    <!--aspectj依赖-->
    <dependency>
      <groupId>org.aspectj</groupId>
      <artifactId>aspectjrt</artifactId>
      <version>${aspectj.version}</version>
    </dependency>
    <dependency>
      <groupId>org.aspectj</groupId>
      <artifactId>aspectjweaver</artifactId>
      <version>${aspectj.version}</version>
    </dependency>
    <!--spring-test依赖-->
    <dependency>
      <groupId>org.springframework</groupId>
      <artifactId>spring-test</artifactId>
      <version>${spring.version}</version>
    </dependency>
    <!--junit依赖-->
    <dependency>
      <groupId>junit</groupId>
      <artifactId>junit</artifactId>
      <version>${junit.version}</version>
      <scope>test</scope>
    </dependency>
    <!-- log4j日志依赖 -->
    <dependency>
      <groupId>org.slf4j</groupId>
      <artifactId>slf4j-api</artifactId>
      <version>${slf4j.version}</version>
    </dependency>
    <dependency>
      <groupId>org.slf4j</groupId>
      <artifactId>slf4j-log4j12</artifactId>
      <version>${slf4j.version}</version>
    </dependency>
    <dependency>
      <groupId>log4j</groupId>
      <artifactId>log4j</artifactId>
      <version>${log4j.version}</version>
    </dependency>
    <dependency>
      <groupId>log4j</groupId>
      <artifactId>log4j</artifactId>
      <version>RELEASE</version>
      <scope>compile</scope>
    </dependency>
  </dependencies>

  <build>
    <pluginManagement><!-- lock down plugins versions to avoid using Maven defaults (may be moved to parent pom) -->
      <plugins>
        <!-- clean lifecycle, see https://maven.apache.org/ref/current/maven-core/lifecycles.html#clean_Lifecycle -->
        <plugin>
          <artifactId>maven-clean-plugin</artifactId>
          <version>3.1.0</version>
        </plugin>
        <!-- default lifecycle, jar packaging: see https://maven.apache.org/ref/current/maven-core/default-bindings.html#Plugin_bindings_for_jar_packaging -->
        <plugin>
          <artifactId>maven-resources-plugin</artifactId>
          <version>3.0.2</version>
        </plugin>
        <plugin>
          <artifactId>maven-compiler-plugin</artifactId>
          <version>3.8.0</version>
        </plugin>
        <plugin>
          <artifactId>maven-surefire-plugin</artifactId>
          <version>2.22.1</version>
        </plugin>
        <plugin>
          <artifactId>maven-jar-plugin</artifactId>
          <version>3.0.2</version>
        </plugin>
        <plugin>
          <artifactId>maven-install-plugin</artifactId>
          <version>2.5.2</version>
        </plugin>
        <plugin>
          <artifactId>maven-deploy-plugin</artifactId>
          <version>2.8.2</version>
        </plugin>
        <!-- site lifecycle, see https://maven.apache.org/ref/current/maven-core/lifecycles.html#site_Lifecycle -->
        <plugin>
          <artifactId>maven-site-plugin</artifactId>
          <version>3.7.1</version>
        </plugin>
        <plugin>
          <artifactId>maven-project-info-reports-plugin</artifactId>
          <version>3.0.0</version>
        </plugin>
      </plugins>
    </pluginManagement>
    <plugins>
      <plugin>
        <groupId>org.apache.maven.plugins</groupId>
        <artifactId>maven-compiler-plugin</artifactId>
        <configuration>
          <source>8</source>
          <target>8</target>
        </configuration>
      </plugin>
    </plugins>
  </build>
</project>
```

#### jdbc.properties

```
jdbc.driverClassName=com.mysql.jdbc.Driver
jdbc.url=jdbc:mysql://localhost:3306/db_spring?useSSL=false&useUnicode=true&characterEncoding=utf-8
jdbc.username=root
jdbc.password=root
```

#### log4j.properties

```
##日志输出的级别，以及配置记录方案
log4j.rootLogger=info, stdout,file

##设置日志记录到控制台的方式
log4j.appender.stdout=org.apache.log4j.ConsoleAppender
log4j.appender.stdout.Target=System.out
log4j.appender.stdout.layout=org.apache.log4j.PatternLayout
log4j.appender.stdout.layout.ConversionPattern=%d{yyyy-MM-dd HH:mm:ss} %5p %c{1}:%L - %m%n
##设置日志记录到文件的方式
log4j.appender.file=org.apache.log4j.FileAppender
log4j.appender.file.File=logs/my_log.log
log4j.appender.file.layout=org.apache.log4j.PatternLayout
log4j.appender.file.layout.ConversionPattern=%d{yyyy-MM-dd HH:mm:ss} %5p %c{1}:%L - %m%n
```

#### spring_mybatis.xml 配置文件

```
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xmlns:tx="http://www.springframework.org/schema/tx"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
        http://www.springframework.org/schema/beans/spring-beans.xsd
        http://www.springframework.org/schema/context
        http://www.springframework.org/schema/context/spring-context.xsd
        http://www.springframework.org/schema/tx
         http://www.springframework.org/schema/tx/spring-tx.xsd">

    <!--读入外部数据库连接属性文件-->
    <context:property-placeholder location="classpath:jdbc.properties"/>

    <!--扫描Service包的组件-->
    <context:component-scan base-package="com.spring.orm.service"/>

    <!--通过druid配置数据源-->
    <bean id="dataSource" class="com.alibaba.druid.pool.DruidDataSource"
          init-method="init" destroy-method="close">
        <property name="driverClassName" value="${jdbc.driverClassName}"/>
        <property name="url" value="${jdbc.url}"/>
        <property name="username" value="${jdbc.username}"/>
        <property name="password" value="${jdbc.password}"/>
        <!-- 初始化大小 -->
        <property name="initialSize" value="20"/>
        <!-- 连接池最大使用连接数量 -->
        <property name="maxActive" value="20"/>
        <!-- 连接池最小空闲 -->
        <property name="minIdle" value="0"/>
        <!-- 配置获取连接等待超时的时间 -->
        <property name="maxWait" value="60000"/>
        <!-- 配置间隔多久才进行一次检测，检测需要关闭的空闲连接，单位是毫秒 -->
        <property name="timeBetweenEvictionRunsMillis" value="60000"/>
        <!-- 配置一个连接在池中最小生存的时间，单位是毫秒 -->
        <property name="minEvictableIdleTimeMillis" value="300000"/>
        <!-- 连接空闲时测试是否有效 -->
        <property name="testWhileIdle" value="false"/>
        <!-- 获取连接时测试是否有效 -->
        <property name="testOnBorrow" value="false"/>
        <!-- 归还连接时是否测试有效 -->
        <property name="testOnReturn" value="false"/>
        <!-- 打开PSCache缓存，并且指定每个连接上PSCache的大小 -->
        <property name="poolPreparedStatements" value="true"/>
        <property name="maxPoolPreparedStatementPerConnectionSize" value="20"/>
    </bean>

    <!-- 配置mybatis的Session -->
    <bean id="sqlSessionFactory" class="org.mybatis.spring.SqlSessionFactoryBean">
        <property name="dataSource" ref="dataSource"/>
        <property name="typeAliasesPackage" value="com.spring.orm.entity"/>
    </bean>

    <!-- 配置通用Mapper -->
    <bean class="tk.mybatis.spring.mapper.MapperScannerConfigurer">
        <property name="basePackage" value="com.spring.orm.dao"/>
        <property name="sqlSessionFactoryBeanName" value="sqlSessionFactory"/>
        <property name="markerInterface" value="com.spring.orm.dao.BaseDAO"/>
        <property name="properties">
            <value>
                mappers = com.spring.orm.dao.BaseDAO
                IDENTITY = MYSQL
            </value>
        </property>
    </bean>

    <!--事务管理bean -->
    <bean id="manager"
          class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
        <property name="dataSource" ref="dataSource"/>
    </bean>
    <!-- 使用声明式事务 -->
    <tx:annotation-driven transaction-manager="manager"/>
</beans>
```

#### User 类

```
package com.spring.orm.entity;

import lombok.Data;

import javax.persistence.GeneratedValue;
import javax.persistence.GenerationType;
import javax.persistence.Id;
import javax.persistence.Table;

@Table(name = "t_user")
@Data
public class User {
    //标注主键和主键生成策略
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;
    private String account;
    private String password;
    private Integer credits;
}
```

#### BaseDAO 接口

```
package com.spring.orm.dao;

import tk.mybatis.mapper.common.Mapper;
import tk.mybatis.mapper.common.MySqlMapper;

//通用的DAO接口
public interface BaseDAO<T> extends Mapper<T>, MySqlMapper<T> {
}
```

#### UserDAO 接口

```
package com.spring.orm.dao;

import com.spring.orm.entity.User;

//UserDAO，继承通用DAO接口基础的CRUD功能
public interface UserDAO extends BaseDAO<User> {
}
```

#### UserService 接口

```
package com.spring.orm.service;

import com.spring.orm.entity.User;

import java.util.List;

public interface UserService {

    //新增用户
    int insertUser(User user);

    //根据id删除用户
    int deleteUser(long id);

    //更新用户
    int updateUser(User user);

    //查询所有用户
    List<User> selectUsers();

    //根据id查询用户
    User getUser(long id);
}
```

#### UserServiceImpl 实现类

```
package com.spring.orm.service.impl;

import com.spring.orm.dao.UserDAO;
import com.spring.orm.entity.User;
import com.spring.orm.service.UserService;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Service;
import org.springframework.transaction.annotation.Transactional;

import java.util.List;

@Service        //标注本类是一个Service组件
@Transactional  //在Service层启动事务
public class UserServiceImpl implements UserService {
    @Autowired
    private UserDAO userDAO;

    @Override
    public int insertUser(User user) {
        return userDAO.insert(user);
    }

    @Override
    public int deleteUser(long id) {
        return userDAO.deleteByPrimaryKey(id);
    }

    @Override
    public int updateUser(User user) {
        return userDAO.updateByPrimaryKey(user);
    }

    @Override
    public List<User> selectUsers() {
        return userDAO.selectAll();
    }

    @Override
    public User getUser(long id) {
        return userDAO.selectByPrimaryKey(id);
    }
}
```

#### 单元测试

```
package com.spring.orm.service.impl;

import com.spring.orm.entity.User;
import com.spring.orm.service.UserService;
import org.junit.Test;
import org.junit.runner.RunWith;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.test.context.ContextConfiguration;
import org.springframework.test.context.junit4.SpringJUnit4ClassRunner;

import java.util.List;

import static org.junit.Assert.*;

@RunWith(SpringJUnit4ClassRunner.class)
@ContextConfiguration(locations = {"/spring_mybatis.xml"})
public class UserServiceImplTest {

    @Autowired
    private UserService userService;

    @Test
    public void insertUser() {
        User user = new User();
        user.setAccount("test");
        user.setPassword("121415");
        user.setCredits(876);
        int n = userService.insertUser(user);
        assertEquals(1, n);
    }

    @Test
    public void deleteUser() {
        int n = userService.deleteUser(1);
        assertEquals(1,n);
    }

    @Test
    public void updateUser() {
        User user = userService.getUser(2);
        user.setPassword("00000000");
        user.setCredits(1111111);
        int n = userService.updateUser(user);
        assertEquals(1, n);
    }

    @Test
    public void selectUsers() {
        List<User> users = userService.selectUsers();
        users.forEach(user -> System.out.println(user));
    }

    @Test
    public void getUser() {
        User user = userService.getUser(1);
        System.out.println(user);
    }
}
```