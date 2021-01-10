## SpringBoot 集成 MyBatis 进行 CRUD

使用 Spring Boot 很大程度上简化了开发，尤其是它的自动化配置，大大的提高了开发效率。现在我们要让 Spring Boot 与 MyBatis 一起工作，更方便我们进行数据库操作。

### 准备工作

#### 选择 Spring Initializr

*注意：* 此处 jdk 版本至少为 8

![](https://upload-images.jianshu.io/upload_images/15141093-164b088b97c86311.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

#### 创建一个 springboot 模块

![](https://upload-images.jianshu.io/upload_images/15141093-c8ec3989078444f6.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

#### 添加所需依赖（我们选择手动添加）

![](https://upload-images.jianshu.io/upload_images/15141093-661dfe80a7e9e364.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

#### 完成构建

![](https://upload-images.jianshu.io/upload_images/15141093-318cc3255ff6a9ee.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

#### 添加所需依赖

```
<dependencies>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter</artifactId>
    </dependency>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-web</artifactId>
    </dependency>

    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-test</artifactId>
        <scope>test</scope>
    </dependency>
    <dependency>
        <groupId>mysql</groupId>
        <artifactId>mysql-connector-java</artifactId>
        <version>5.1.47</version>
    </dependency>

    <dependency>
        <groupId>org.mybatis.spring.boot</groupId>
        <artifactId>mybatis-spring-boot-starter</artifactId>
        <version>1.1.1</version>
    </dependency>

    <dependency>
        <groupId>org.projectlombok</groupId>
        <artifactId>lombok</artifactId>
        <version>1.18.6</version>
        <optional>true</optional>
    </dependency>
</dependencies>
```

#### 在 application.properties 中添加配置

```
# 数据源配置
spring.datasource.url=jdbc:mysql://localhost:3306/db_spring?useSSL=false&useUnicode=true&characterEncoding=utf-8
spring.datasource.username=root
spring.datasource.password=root
spring.datasource.driver-class-name=com.mysql.jdbc.Driver

# 指定实体类映射的包
mybatis.type-aliases-package=com.springboot.mybatis.entity
```

#### 包结构的搭建


### 编写代码

#### 编写 entity 实体类

```
@Data
public class Course {
    private Long courseId;
    private String courseName;
    private Long userId;
    private String courseClass;
    private String cover;
    private String courseCode;
    private Short finished;
}
```

#### 编写 Mapper 接口

在 Mapper 接口中编写 sql 语句，column 为数据库的字段，property 为实体类中属性。为了方便拼接，要在 sql 语句的最后空一格。

```
public interface CourseMapper {
    @Results({
            @Result(property = "courseId", column = "course_id"),
            @Result(property = "courseName", column = "course_name"),
            @Result(property = "userId", column = "user_id"),
            @Result(property = "courseClass", column = "course_class"),
            @Result(property = "cover", column = "cover"),
            @Result(property = "courseCode", column = "course_code"),
            @Result(property = "finished", column = "finished"),
    })
    @Select("SELECT * FROM t_course ") //所有sql语句后加空格
    List<Course> selectAll();

    @Results({@Result(column = "course_id",property = "courseId"),
            @Result(column = "course_name",property = "courseName"),
            @Result(column = "user_id",property = "userId"),
            @Result(column = "course_class",property = "courseClass"),
            @Result(column = "cover",property = "cover"),
            @Result(column = "course_code",property = "courseCode"),
            @Result(column = "finished",property = "finished")
    })
    @Select("SELECT * FROM t_course WHERE course_id = #{courseId} ")
    Course getOne(Long courseId);

    @Delete("DELETE FROM t_course WHERE course_id =#{courseId}")
    void delete(Long courseId);

    @Insert("INSERT INTO t_course(course_name,user_id,course_class,cover,course_code,finished)" +
            " VALUES(#{courseName}, #{userId}, #{courseClass}, #{cover}, #{courseCode}, #{finished}) ")
    void insert(Course course);

    @Update("UPDATE t_course SET cover=#{cover}, finished=#{finished} WHERE course_id=#{courseId}")
    void update(Course course);
}
```

#### 编写 Service 接口

```
public interface CourseService {
    List<Course> selectAll();

    Course getOne(long courseId);

    void delete(long courseId);

    Course insert(Course course);

    void update(Course course);
}

```

#### 编写 ServiceImpl 实现接口中的方法

```
@Service
public class CourseServiceImpl implements CourseService {
    @Resource
    private CourseMapper courseMapper;

    @Override
    public List<Course> selectAll() {
        return courseMapper.selectAll();
    }

    @Override
    public Course getOne(long courseId) {
        return courseMapper.getOne(courseId);
    }

    @Override
    public void delete(long courseId) {
        courseMapper.delete(courseId);
    }

    @Override
    public Course insert(Course course) {
        courseMapper.insert(course);
        return course;
    }

    @Override
    public void update(Course course) {
        courseMapper.update(course);
    }
}
```

#### 对 ServiceImpl 进行单元测试

通过后方可编写控制层的代码

```
@RunWith(SpringRunner.class)
@SpringBootTest
public class CourseServiceImplTest {
    @Resource
    private CourseService courseService;

    @Test
    public void selectAll() {
        List<Course> courseList = courseService.selectAll();
        courseList.forEach(course -> System.out.println(course));
    }

    @Test
    public void getOne() {
        Course course = courseService.getOne(1L);
        System.out.println(course);
    }

    @Test
    public void delete() {
        courseService.delete(9L);
    }

    @Test
    public void insert() {
        Course course = new Course();
        course.setCourseName("微信小程序开发");
        course.setCourseName("软件1721");
        course.setUserId(1L);
        course.setCover("1.jpg");
        course.setCourseCode(RandomUtil.getRandomCode());
        course.setFinished((short) 0);
        courseService.insert(course);
    }

    @Test
    public void update() {
        Course course = courseService.getOne(12L);
        course.setCover("cover.jpg");
        course.setFinished((short) 0);
        courseService.update(course);
    }
}
```

#### 在 controller 中写入 RESTful 请求

```
@RestController
@RequestMapping(value = "/api")
public class CourseController {
    @Resource
    private CourseService courseService;

    @RequestMapping(value = "/courses", method = RequestMethod.GET)
    public List<Course> selectAll() {
        return courseService.selectAll();
    }

    @RequestMapping(value = "/course/{id}", method = RequestMethod.GET)
    public Course getOne(@PathVariable("id") long id) {
        return courseService.getOne(id);
    }

    @RequestMapping(value = "/course/{id}", method = RequestMethod.DELETE)
    public void deleteCourse(@PathVariable("id") long id) {
        courseService.delete(id);
    }

    @RequestMapping(value = "/course", method = RequestMethod.POST)
    public Course addCourse(@RequestBody Course course) {
        course.setCourseCode(RandomUtil.getRandomCode());
        return courseService.insert(course);
    }

    @RequestMapping(value = "/course", method = RequestMethod.PUT)
    public void updateCourse(@RequestBody Course course) {
        courseService.update(course);
    }
}
```

### 运行和验证

#### 运行

打开启动类，记得加上注释

```
@SpringBootApplication
@MapperScan("com.springboot.mybatis.mapper")
public class SpringBootMybatisApplication {

    public static void main(String[] args) {
        SpringApplication.run(SpringBootMybatisApplication.class, args);
    }

}
```

#### 验证

* 弄清 get，put，delete，post 等请求的区别
* put 和 post 操作注意：

![](https://upload-images.jianshu.io/upload_images/15141093-91e34d4b2a4e28ee.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


