前后端分离已成为互联网项目开发的业界标准使用方式，在项目中，我们会使用 Spring MVC 为前端页面提供接口，来实现项目的前后端分离。Spring MVC 是一个模型 - 视图 - 控制器（MVC）的 Web 框架，Spring MVC 是 Spring 产品组合的一部分，它享有 Spring IoC 容器紧密结合 Spring 松耦合等特点。那我们如何使用 Spring MVC 开发一个前后分离的项目呢？

### 后端

#### 根据需求，设计数据库，建库、建表，准备数据

![](https://upload-images.jianshu.io/upload_images/15141093-9ba2b0460f3762b9.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

主键使用 bigint，实体类中的属性使用包装类，例如：Long

#### 建立 Web 模块，选择 WebAPP 类型的 maven 项目

![](https://upload-images.jianshu.io/upload_images/15301391-47f37697771e933e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/963/format/webp)

#### 手动创建 src、resources、test/java 目录

#### 建立相应的package （entity、dao、service、controller）

![](https://upload-images.jianshu.io/upload_images/15141093-cd8e601352ef6636.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

#### pom 依赖：web 模块依赖、webMVC 依赖、jackson 相关依赖

```
<properties>
    <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
    <maven.compiler.source>1.8</maven.compiler.source>
    <maven.compiler.target>1.8</maven.compiler.target>
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
    <lombok.version>1.18.6</lombok.version>
    <jackson.version>2.9.8</jackson.version>
    <jackson-mapper.version>1.9.13</jackson-mapper.version>
  </properties>
```

#### entity 实体类

在实体类中,一个类对应一张表,这里我们通过注释引用数据库中表
```
@Table(name = "t_course")
@Data
public class Course {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long courseId;
    private String courseName;
    private Long userId;
    private String courseClass;
    private String cover;
    private String courseCode;
    private Short finished;
}
```

#### dao 接口，增加自定义的复杂关联查询（注解）

```
public interface CourseDAO extends BaseDAO<Course> {

    //自定义的多表关联查询
    @Results({
            @Result(column = "course_id", property = "courseId"),
            @Result(column = "course_name", property = "courseName"),
            @Result(column = "user_id", property = "userId"),
            @Result(column = "course_class", property = "courseClass"),
            @Result(column = "cover", property = "cover"),
            @Result(column = "course_code", property = "courseCode"),
            @Result(column = "finished", property = "finished"),
            @Result(column = "username", property = "username"),
            @Result(column = "avatar", property = "avatar")
    })
    //此处的sql语句一定要先在数据库中调通
    @Select("SELECT a.*,b.username,b.avatar FROM t_course a Left JOIN t_sys_user b ON a.user_id=b.user_id WHERE a.finished = 0")
    List<CourseVO> selectCurrentCourses();
}
```
#### service 接口，注入dao，调用相应方法

```
public interface CourseService {
    List<CourseVO> selectCurrentCourses();
}
```
```
@Service
@Transactional
public class CourseServiceImpl implements CourseService {
    @Autowired
    private CourseDAO courseDAO;

    @Override
    public List<CourseVO> selectCurrentCourses() {
        return courseDAO.selectCurrentCourses();
    }
}
```
#### 对 service 做单元测试（JUnit）

```
@RunWith(SpringJUnit4ClassRunner.class)
@ContextConfiguration(locations = {"/spring_mybatis.xml"})
public class CourseServiceImplTest {
    @Autowired
    private CourseService courseService;

    @Test
    public void selectCurrentCourses() {
        List<CourseVO> courseVOList = courseService.selectCurrentCourses();
        courseVOList.forEach(courseVO -> System.out.println(courseVO));
    }
}
```

#### controller，使用 RESTful 风格请求，完成控制层

```
@RestController
public class CourseController {
    @Autowired
    private CourseService courseService;


    @RequestMapping(value = "courses", method = RequestMethod.GET)
    public List<CourseVO> selectCourses() {
        List<CourseVO> courseVOList = courseService.selectCurrentCourses();
        return courseVOList;
    }
}
```

#### 对 controller 进行测试（postman），杜绝一切 404 和 500

### 前端

使用 vue.js 实现获取接口
```
<!-- 通过CDN引入axios -->
<script src="https://unpkg.com/axios/dist/axios.min.js"></script>
```
```
created: function() {
    //记录当前的vue对象
    var _this = this;
    //通过axios发起异步请求
    axios.get('连接接口的网址')
        //回调函数
        .then(function(response) {
            console.log(response.data);
            _this.courses = response.data;
        })
}
```
将调用到的数据放入一组数据中，如 courses[] ,之后就能在前端使用 courses[] 中的数据。

[项目地址](https://github.com/iamliuyu/spring-study/tree/master/web)