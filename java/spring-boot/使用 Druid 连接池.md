## 使用 Druid 连接池

DRUID 是阿里巴巴开源平台上一个数据库连接池实现，它结合了 C3P0、DBCP、PROXOOL 等 DB 池的优点，同时加入了日志监控，可以很好的监控 DB 池连接和 SQL 的执行情况。

#### 添加 pom 依赖

```
<!-- druid -->
<dependency>
    <groupId>com.alibaba</groupId>
    <artifactId>druid-spring-boot-starter</artifactId>
    <version>1.1.9</version>
</dependency>
```

#### 数据源配置

在 `application.properties` 配置文件里添加 `druid` 的配置

```
## 数据源配置
#spring.datasource.url=jdbc:mysql://localhost:3306/springboot?useUnicode=true&characterEncoding=utf-8&useSSL=false
#spring.datasource.username=root
#spring.datasource.password=root
#spring.datasource.driver-class-name=com.mysql.jdbc.Driver

# 这4个参数key里不带druid也可以，即可以还用上面的这个4个参数
spring.datasource.druid.url=jdbc:mysql://localhost:3306/springboot?useUnicode=true&characterEncoding=utf-8&useSSL=false
spring.datasource.druid.username=root
spring.datasource.druid.password=root
spring.datasource.druid.driver-class-name=com.mysql.jdbc.Driver

# 初始化时建立物理连接的个数
spring.datasource.druid.initial-size=5
# 最大连接池数量
spring.datasource.druid.max-active=30
# 最小连接池数量
spring.datasource.druid.min-idle=5
# 获取连接时最大等待时间，单位毫秒
spring.datasource.druid.max-wait=60000
# 配置间隔多久才进行一次检测，检测需要关闭的空闲连接，单位是毫秒
spring.datasource.druid.time-between-eviction-runs-millis=60000
# 连接保持空闲而不被驱逐的最小时间
spring.datasource.druid.min-evictable-idle-time-millis=300000
# 用来检测连接是否有效的sql，要求是一个查询语句
spring.datasource.druid.validation-query=SELECT 1 FROM DUAL
# 建议配置为true，不影响性能，并且保证安全性。申请连接的时候检测，如果空闲时间大于timeBetweenEvictionRunsMillis，执行validationQuery检测连接是否有效。
spring.datasource.druid.test-while-idle=true
# 申请连接时执行validationQuery检测连接是否有效，做了这个配置会降低性能。
spring.datasource.druid.test-on-borrow=false
# 归还连接时执行validationQuery检测连接是否有效，做了这个配置会降低性能。
spring.datasource.druid.test-on-return=false
# 是否缓存preparedStatement，也就是PSCache。PSCache对支持游标的数据库性能提升巨大，比如说oracle。在mysql下建议关闭。
spring.datasource.druid.pool-prepared-statements=true
# 要启用PSCache，必须配置大于0，当大于0时，poolPreparedStatements自动触发修改为true。
spring.datasource.druid.max-pool-prepared-statement-per-connection-size=50
# 配置监控统计拦截的filters，去掉后监控界面sql无法统计
spring.datasource.druid.filters=stat,wall
# 通过connectProperties属性来打开mergeSql功能；慢SQL记录
spring.datasource.druid.connection-properties=druid.stat.mergeSql=true;druid.stat.slowSqlMillis=500
# 合并多个DruidDataSource的监控数据
spring.datasource.druid.use-global-data-source-stat=true
```

#### 测试

浏览器输入 `http://localhost:8080/druid` ，界面如下

![](https://i.loli.net/2019/04/18/5cb82b0171042.png)

#### druid 监控

浏览器输入 `http://localhost:8080/druid` 直接就能看到 druid 控制台界面，在这里面可以看到很多项目信息，如果任凭用户随意访问，非常危险。我们可以通过配置，设置只有通过登录认证才可以访问

在 `application.properties` 配置文件中增加

```
# druid连接池监控
spring.datasource.druid.stat-view-servlet.login-username=admin
spring.datasource.druid.stat-view-servlet.login-password=123
# 排除一些静态资源，以提高效率
spring.datasource.druid.web-stat-filter.exclusions=*.js,*.gif,*.jpg,*.png,*.css,*.ico,/druid/*
```