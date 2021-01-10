## 采用注解开发 bean

传统的Spring使用.xml文件来对bean进行注入或者是配置aop，这么做导致.xml文件变得十分庞大，导致配置文件的可读性与可维护性变得很低。开发中在.java文件和.xml文件之间不断切换也会降低开发的效率。为了解决这些问题，Spring引入了注解，通过"@XXX"的方式，让注解与Java Bean紧密结合，既减少了配置文件的体积，又增加了Java Bean的可读性。

### Component注解：启用组件扫描基础包

Component注解的功能就是把普通的POJO类实例化到Spring的IOC容器中,就是定义成`<bean id="" class="">`

#### 使用@Component注解

在此我们创建一个Hello类，使用@Component注解：

```
package com.spring.annotation;

import org.springframework.stereotype.Component;

/**
 *采用注解开发的bean
 * @Component用于类级别的注解，标注本类为一个可被spring容器托管的bean
 */
@Component
public class Hello {
    public String getHello() {
        return "Hello World";
    }
}
```

#### 使用@ComponentScan注解

创建一个HelloApp类，使用@ComponentScan注解:

```
package com.spring.annotation;

import org.springframework.context.ApplicationContext;
import org.springframework.context.annotation.AnnotationConfigApplicationContext;
import org.springframework.context.annotation.ComponentScan;

/*
* @ComponentScan
* 用于寻找@Component标注的bean
* */
@ComponentScan
public class HelloApp {
    public static void main(String[] args) {
        //1.通过注解创建上下文
        ApplicationContext context = new AnnotationConfigApplicationContext(Hello.class);
        //2.读取bean
        Hello hello = context.getBean(Hello.class);
        //3.运行
        System.out.println(hello.getHello());
    }
}
```

运行：
![](https://upload-images.jianshu.io/upload_images/3388039-ca2ece92e9ceea91.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1000/format/webp)

### 使用Lombok插件，简化POJO类

#### Lombok插件的使用

* Settings->plugins，搜索Lombok，安装，重启IDEA
* 添加依赖
```
<!--Lombok依赖-->
<dependency>
    <groupId>org.projectlombok</groupId>
    <artifactId>lombok</artifactId>
    <version>1.16.18</version>
</dependency>
```
* 使用@Data注解，不用再写那些构造方法、getter/setter,toString()了，专注定义属性即可

#### 使用@Data注解

在此我们创建一个Phone类,使用@Data和@Component注解：
```
package com.spring.annotation;

import lombok.Data;
import org.springframework.beans.factory.annotation.Value;
import org.springframework.stereotype.Component;

//使用注解和Lombok开发的Phone类
@Component
@Data
public class Phone {
    @Value("iPhone")
    private String brand;

    @Value("6666.66")
    private double price;
}
```

Student类
```
package com.spring.annotation;

import lombok.Data;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.beans.factory.annotation.Value;
import org.springframework.stereotype.Component;


@Component
@Data
public class Student {
    @Value("Tom")
    private String name;

    @Value("21")
    private int age;

    //使用 @Autowired注入一个Phone类的bean
    @Autowired
    private Phone phone;
}
```

StudentApp类
```
package com.spring.annotation;

import org.springframework.context.ApplicationContext;
import org.springframework.context.annotation.AnnotationConfigApplicationContext;
import org.springframework.context.annotation.ComponentScan;

@ComponentScan
public class StudentApp {
    public static void main(String[] args) {
        ApplicationContext context = new AnnotationConfigApplicationContext(StudentApp.class);
        Student student = context.getBean(Student.class);
        System.out.println(student);
    }
}
```

运行：
![](https://upload-images.jianshu.io/upload_images/3388039-37ef8eeaf63bb2fc.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1000/format/webp)





