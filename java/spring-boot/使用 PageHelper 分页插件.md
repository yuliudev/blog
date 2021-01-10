## 使用 PageHelper 分页插件

在上拉加载或后台管理等场景中，由于数据库的数据比较多，则需要为其做分页。PageHelper 插件很好地实现了分页的功能，我们只需要做查询即可，不再需要手动对查询数据进行分页。

#### 添加 pom 依赖

```
<!-- pagehelper -->
<dependency>
    <groupId>com.github.pagehelper</groupId>
    <artifactId>pagehelper-spring-boot-starter</artifactId>
    <version>1.2.5</version>
</dependency>
```

#### 修改配置文件

在 `application.properties` 配置文件中添加以下配置

```
#pagehelper分页插件配置
pagehelper.helperDialect=mysql
#当reasonable:true时在pageNum<1会查询第一页，如果pageNum>pages会查询最后一页
#也就是说当pageNum>你的最大页数时会返回最后一页的数据而不是返回null
#禁用合理化时，如果pageNum<1或pageNum>pages会返回空数据
pagehelper.reasonable=false
pagehelper.supportMethodsArguments=true
pagehelper.params=count=countSql
```

#### 修改 Controller

```
@GetMapping(value = "/list")
public ResponseResult getAll(@RequestParam(defaultValue = "1") int pageNo, @RequestParam(defaultValue = "10") int pageSize) {
    PageHelper.startPage(pageNo,pageSize);
    List<ArticleVO> articleList = articleService.selectAll();
    return ResponseResult.success(articleList);
}
```

* `pageNo`和`pageSize`两个参数是为了接收前台传过来的值，并且通过`defaultValue`为这两个参数提供了默认值。
* 分页主要代码：`PageHelper.startPage(pageNo,pageSize);`

需要注意的是，分页代码`PageHelper.startPage(pageNo,pageSize);`只对其后的第一个查询有效

#### 测试

启动服务，在 swagger 中进行测试,可以看到只查询了第一页 5 条数据

![](https://i.loli.net/2019/04/18/5cb8228e39d82.png)

#### 返回分页信息

上面我们返回的只是数据，而总页数、当前页数、每页条数等分页相关的信息并没有返回

下面我们对`controller`、`service`、`mapper`里的方法的返回值做一下修改，将`List<Article>`改为`Page<Article>`，`Page`是`com.github.pagehelper`包里的类，它是`java.util.ArrayList`的子类

* ArticleMapper：将返回值修改为Page<ArticleVO>
* ArticleService、ArticleServiceImpl：将返回值修改为Page<ArticleVO>
* 用`com.github.pagehelper.PageInfo`类封装 Page<ArticleVO> 数据
```
@GetMapping(value = "/list")
public ResponseResult getAll(@RequestParam(defaultValue = "1") int pageNo, @RequestParam(defaultValue = "10") int pageSize) {
    PageHelper.startPage(pageNo,pageSize);
    PageInfo<ArticleVO> articleVOPageInfo = new PageInfo<>(articleService.selectAll());
    return ResponseResult.success(articleVOPageInfo);
}
```

启动服务，在 swagger 中进行测试,可以看到只查询了第一页 5 条数据，并且包含了分页相关的信息

[uni-app实现上拉加载，下拉刷新（下拉带动画）](https://blog.csdn.net/qq_39197547/article/details/84832913)
