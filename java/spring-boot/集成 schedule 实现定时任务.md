## 集成 schedule 实现定时任务

在项目开发过程中，我们经常需要执行具有周期性的任务。通过定时任务可以很好的帮助我们实现。在 spirngboot 中使用定时任务变的特别简单，只需要在启动类上增加一个 `@EnableScheduling` 注解即可。
![](https://i.loli.net/2019/04/16/5cb5cc03a5e29.png)

### springboot 集成 schedule

#### 添加 maven 依赖

由于 Spring Schedule 包含在 `spring-boot-starter` 基础模块中，所以不需要增加额外的依赖

#### 添加启动注解

在 springboot 入口或者配置类中增加 `@EnableScheduling` 注解即可启用定时任务

```
@SpringBootApplication
@MapperScan("com.springboot.jianyue.api.mapper")
@EnableSwagger2Doc
@EnableScheduling
public class JianyueApiApplication {

    public static void main(String[] args) {
        SpringApplication.run(JianyueApiApplication.class, args);
    }

}
```

#### 添加定时任务

`@Scheduled` 所支持的参数

* cron：cron 表达式，指定任务在特定时间执行 

* fixedDelay：表示上一次任务执行完成后多久再次执行，参数类型为 long，单位 ms 

* fixedDelayString：与 fixedDelay 含义一样，只是参数类型变为 String

* fixedRate：表示按一定的频率执行任务，参数类型为 long，单位 ms

* fixedRateString: 与 fixedRate 的含义一样，只是将参数类型变为 String

* initialDelay：表示延迟多久再第一次执行任务，参数类型为 long，单位 ms

* initialDelayString：与 initialDelay 的含义一样，只是将参数类型变为 String

* zone：时区，默认为当前时区，一般没有用到

##### Cron 表达式

Cron 表达式由 6 或 7 个空格分隔的时间字段组成

添加一个 reportCurrentTime1() 方法，每 10 秒执行一次

```
@Component
public class ScheduledTasks {
    @Scheduled(cron = "0/10 * * * * *")
    public void reportCurrentTime1() {
        log.info("cron:" + new Date());
    }
}
```

注：当方法的执行时间超过任务调度频率时，调度器会在下个周期执行

> cron语法

常用： 秒、分、时、日、月、年

0 0 10,14,16 * * ? 每天上午 10 点，下午 2 点，4 点
0 0 12 * * ? 每天中午 12 点触发
0 0/5 0 * * ? 每 5 分钟执行一次

更多语法请参考：[cron 表达式详解](https://www.cnblogs.com/linjiqin/archive/2013/07/08/3178452.html)

##### fixedDelay

添加一个 reportCurrentTime2() 方法，上一次执行完毕时间点之后 5 秒再执行，距上次执行完成后间隔 5 秒开始执行下次任务

```
    @Scheduled(fixedDelay = 5000)
    public void reportCurrentTime2() {
        log.info("fixedDelay:" + new Date());
    }
```

##### fixedRate

添加一个 reportCurrentTime3() 方法，上一次开始执行时间点之后 5 秒再执行，每次执行间隔时间 5 秒

```
    @Scheduled(fixedRate = 5000)
    public void reportCurrentTime3() {
        log.info("fixedRate :" + new Date());
    }
```

注：当方法的执行时间超过任务调度频率时，调度器会在当前方法执行完成后立即执行下次任务

##### initialDelay

添加一个 reportCurrentTime4() 方法，第一次延迟 1 秒后执行，之后按 fixedRate 的规则每 5 秒执行一次

```
    @Scheduled(initialDelay = 1000, fixedDelay = 5000)
    public void reportCurrentTime4() {
        log.info("fixedDelay:" + new Date());
    }
```

### 配置 TaskScheduler 线程池

如果使用串行式，所有的定时任务都是通过一个线程执行，这样当某个定时任务出问题时，整个都会崩溃。在实际项目中，一个系统可能会定义多个定时任务，多个定时任务之间是可以相互独立且可以并行执行的

#### 自定义线程池

新增一个配置类，实现 `SchedulingConfigurer` 接口，重写 `configureTasks` 方法，通过 `taskRegistrar` 设置自定义线程池

```
@Configuration
public class ScheduledConfig implements SchedulingConfigurer {
   @Override
   public void configureTasks(ScheduledTaskRegistrar scheduledTaskRegistrar) {
       scheduledTaskRegistrar.setScheduler(setTaskExecutors());
   }

   @Bean()
   public Executor setTaskExecutors(){
       //创建一个基本大小为3的线程池
       return Executors.newScheduledThreadPool(3);
   }
}
```

然后运行主类即可,可以看到每个任务都是用不同的线程来执行，这样就比较安全了。





