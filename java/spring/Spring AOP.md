AOP（Aspect Oriented Programming，面向切面编程），通过提供另一种思考程序的方式来补充OOP（Object Oriented Programming，面向对象编程）。AOP是横向抽取，OOP是纵向抽象。

* 定义：在运行时，动态地将代码切入到类的指定`方法`、指定位置的编程思想就是面向切面的编程。

* 主要应用场景：收集日志、事务处理、安全检测。

### AOP 基本上是通过代理机制的实现

> 代理模式的例子

编写 Move 接口
```
package com.spring.aop;

public interface Move {
    void move();
}
```

编写 Tank 类，实现 Move 接口
```
package com.spring.aop;

public class Tank implements Move {
    @Override
    public void move() {
        System.out.println("Tank is moving!");
    }
}
```

编写 TankProxy 类
```
package com.spring.aop;

public class TankProxy implements Move{
    private Move t;

    public TankProxy (Move t) {
        this.t = t;
    }


    @Override
    public void move() {
        System.out.println("start");
        t.move();
        System.out.println("stop");
    }
}
```

运行
```
package com.spring.aop;

public class MoveApp {
    public static void main(String[] args) {
        Move t1 = new Tank();
        Move moveProxy = new TankProxy(t1);
        moveProxy.move();
    }
}
```

注意：**代理不能代理具体的类，只能代理接口。**

### 理解AOP中的几个概念

* Aspect（切面）：将关注点进行模块化。
* Join Point（连接点）：在程序执行过程中的某个特定的点,如谋方法调用时或处理异常时。
* Advice（通知）：在切面的某个特定的连接点上执行的动作。
* PointCut（切入点）：匹配连接点的断言。
* Introduction（引入）：声明额外的方法或某个类型的字段。
* Target Object（目标对象）：被一个或多个切面所通知的对象。
* AOP Proxy（AOP代理）：AOP框架创建的对象,用来实现 Aspect Contract 包括通知方法执行等功能。
* Weaving（织入）：把切面连接到其他的应用程序类型或对象上,并创建一个 Advice 的对象。

其中，Advice 的主要类型有:

* Before Advice（前置通知）
* After Returning Advice(返回后通知）
* After Throwing Advice（抛出异常后通知）
* After （finally）Advice（最后通知）
* Around Advice（环绕通知）

**切入点和连接点的匹配，是AOP的关键**

### Spring AOP

* Spring AOP 用纯 Java 实现，目前仅支持方法调用作为连接点。
* Spring AOP 通常和 Spring IoC 容器一起使用。

### Hello 的前置增强练习

#### pom.xml 中添加 AOP 相关依赖

```
<!--spring-aop依赖-->
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
```

#### Hello 接口和实现类

```
package com.spring.aop;

public interface Hello {
    String sayHello();
}
```
```
package com.spring.aop;

public class HelloImpl implements Hello {
    @Override
    public String sayHello() {
        return "Spring AOP";
    }
}
```

#### MyBefore 类

```
package com.spring.aop;

//用戶自定义前置增强类
public class MyBefore {
    public void beforeMethod() {
        System.out.println("This is a before method...");
    }
 }
 ```

 #### 配置文件

 ```
<bean id="hello" class="com.spring.aop.HelloImpl"/>
<bean id="myBefore" class="com.spring.aop.MyBefore"/>
<aop:config>
    <aop:aspect id="before" ref="myBefore">
        <aop:pointcut id="myPointcut" expression="execution(* com.spring.aop.*.*(..))"/>
        <aop:before method="beforeMethod" pointcut-ref="myPointcut"/>
    </aop:aspect>
</aop:config>
```

#### 应用主类

```
package com.spring.aop;

import org.springframework.context.ApplicationContext;
import org.springframework.context.support.ClassPathXmlApplicationContext;

public class HelloApp {
    public static void main(String[] args) {
        ApplicationContext ac = new ClassPathXmlApplicationContext("applicationContext.xml");
        Hello hello = (Hello) ac.getBean("hello");
        System.out.println(hello.sayHello());
    }
}
```