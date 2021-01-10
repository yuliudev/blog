## 使用 swagger2 构建在线接口文档

编写和维护接口文档是每个程序员的职责，根据 Swagger2 可以快速帮助我们编写最新的 API 接口文档，间接提升了团队开发的沟通效率。

### swagger UI 使用方法

#### 在 pom.xml 中引入依赖

```
<dependency>
    <groupId>com.spring4all</groupId>
    <artifactId>swagger-spring-boot-starter</artifactId>
    <version>1.8.0.RELEASE</version>
</dependency>
```

#### 在应用主类中增加 @EnableSwagger2Doc 注解

```
@SpringBootApplication
@MapperScan("com.springboot.mybatis.mapper")
@EnableSwagger2Doc
public class SpringBootMybatisApplication {

    public static void main(String[] args) {
        SpringApplication.run(SpringBootMybatisApplication.class, args);
    }

}
```

默认情况下就能产生所有当前 SpringMVC 加载的请求映射文档。

访问地址：http://localhost:8080/swagger-ui.html


#### 在配置文件中添加 swagger2 配置

```
#swagger配置
swagger.enabled=true
swagger.title=spring-boot-mybatis module api
swagger.description=Starter for swagger 2.x
swagger.license=Apache License, Version 2.0
swagger.licenseUrl=https://www.apache.org/licenses/LICENSE-2.0.html
swagger.termsOfServiceUrl=https://github.com/dyc87112/spring-boot-starter-swagger
swagger.contact.name=（此处自行填写name）
swagger.contact.url=（此处自行填写url地址）
swagger.contact.email=（此处自行填写email）
swagger.base-package=com.springboot.mybatis.controller
swagger.base-path=/**
swagger.exclude-path=/error, /ops/**
```

### swagger UI 整体效果

![](https://upload-images.jianshu.io/upload_images/15141093-6bd5a8ac3b63ca6c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

更多特性请移步该项目：[github地址](https://github.com/SpringForAll/spring-boot-starter-swagger)