## IoC 练习

控制反转（Inversion of Control，缩写为IoC），是面向对象编程中的一种设计原则，可以用来减低计算机代码之间的耦合度。其中最常见的方式叫做依赖注入（Dependency Injection，简称DI），还有一种方式叫“依赖查找”（Dependency Lookup）。通过控制反转，对象在被创建的时候，由一个调控系统内所有对象的外界实体，将其所依赖的对象的引用传递给它。也可以说，依赖被注入到对象中。依赖注入是对“控制反转”的不同说法，本质是一回事。

### IoC容器能干什么

IoC是一种设计思想，帮助我们实现程序之间的解耦，设计出耦合性更低更优良的的程序，传统的开发模式在程序类的内部主动的依赖对象（new Object）来实现注入，从而使的类之间高度耦合，有了IoC容器之后，我们可以把对象的控制权交给容器，让容器为我们创建管理对象，这样，对象之间耦合度变低，程序的架构体系也会更加的灵活。

### IoC容器和bean

Spring通过IoC容器来管理所有Java对象及其相互之间的依赖关系。IoC容器在创建bean的时候，会注入其依赖项。IoC的应用有以下两种设计模式：

* 反射：在运行状态中，根据提供的类的路径或类名，通过反射来动态获取该类的所有属性和方法。
* 工厂模式：把IoC容器当做一个工厂，在配置文件或注解中给出定义，然后利用反射技术，根据给出的类名生成相应的对象。对象生成的代码及对象之间的依赖关系在配置文件中定义，实现了解耦。

Spring IoC容器的核心基础包：

* org.springframework.beans
* org.springframework.context

### 配置和使用

**xml**形式
```
<bean id = "..." class="...">
   <!-- 放置这个bean的协作者和配置 -->
</bean>
```

**注解**形式
```
@Configuration
public class AppConfig{
    @Bean
    public MyService myService(){
          return new MyServiceImpl();
   }
}
```

### 注入方式

* 构造器注入
* setter注入

### 实战：依赖注入的例子

**消息服务接口和实现类**
```
public interface MessageService {
   String getMessage();
}
```
```
public class MessageServiceImpl implements MessageService {
    private String username;
    private int age;

    public MessageServiceImpl(String username, int age) {
        this.username = username;
        this.age = age;
    }

    @Override
    public String getMessage() {
        return "Hello World!" + "\nusername is " + username + ",age is " + age;
    }
}
```

**打印器类**
```
public class MessagePrinter {
    final private MessageService messageService;

    public MessagePrinter(MessageService messageService) {
        this.messageService = messageService;
    }

    public void printMessage() {
        System.out.println(this.messageService.getMessage());
    }
}
```

**配置文件**
```
<!--定义bean,并使用构造器注入-->
<bean id="messageServiceImpl" class="com.spring.quickstart.MessageServiceImpl">
    <constructor-arg name="username" value="Tom"/>
    <constructor-arg name="age" value="20"/>
</bean>

<bean id="messagePrinter" class="com.spring.quickstart.MessagePrinter">
    <constructor-arg name="messageService" ref="messageServiceImpl"/>
</bean>
```

**应用主类**
```
public class MessageApp {
    public static void main(String[] args) {
        ApplicationContext context = new ClassPathXmlApplicationContext("/applicationContext.xml");
        MessagePrinter messagePrinter = context.getBean(MessagePrinter.class);
        messagePrinter.printMessage();
    }
}
```




