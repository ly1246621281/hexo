---
categories:
  - JAVA总结
  - JAVA（2020--2023）
---
# @EnableConfigurationProperties 注解

## 1.@EnableConfigurationProperties 作用

作用是为了激活@ConfigurationProperties 。作用同在配置类上@Component。

当`@EnableConfigurationProperties`注解应用到你的`@Configuration`时， 任何被`@ConfigurationProperties`注解的beans将自动被Environment属性配置。 这种风格的配置特别适合与SpringApplication的外部YAML配置进行配合使用



## 2.@ConfigurationProperties 作用

@ConfigurationProperties 是使配置文件变成Bean对象，作用同@Value。

注解可以放置在类和方法上。

> 当将该注解作用于方法上时，如果想要有效的绑定配置，那么该方法需要有@Bean注解且所属Class需要有@Configuration注解。
>
> **简单一句话概括就是：Sring的有效运行是通过上下文（Bean容器）中Bean的配合完成的，Bean可以简单理解成对象，有些对象需要指定字段内容，那么这些内容我们可以通过配置文件进行绑定，然后将此Bean归还给容器**

```java
@Configuration
public class DruidDataSourceConfig {
    /**
     * DataSource 配置
     * @return
     */
    @ConfigurationProperties(prefix = "spring.datasource.druid.read")
    @Bean(name = "readDruidDataSource")
    public DataSource readDruidDataSource() {
        return new DruidDataSource();
    }


    /**
     * DataSource 配置
     * @return
     */
    @ConfigurationProperties(prefix = "spring.datasource.druid.write")
    @Bean(name = "writeDruidDataSource")
    @Primary
    public DataSource writeDruidDataSource() {
        return new DruidDataSource();
    }
}
```

作用于类上时：

```java
@ConfigurationProperties(prefix = "spring.datasource")
@Component
@Data
public class DatasourcePro {

    private String url;

    private String username;

    private String password;

    // 配置文件中是driver-class-name, 转驼峰命名便可以绑定成
    private String driverClassName;

    private String type;
}
```

### 总结

1. @ConfigurationProperties 和 @value 有着相同的功能,但是 @ConfigurationProperties的写法更为方便
2. @ConfigurationProperties 的 POJO类的命名比较严格,因为它必须和prefix的后缀名要一致, 不然值会绑定不上, 特殊的后缀名是“driver-class-name”这种带横杠的情况,在POJO里面的命名规则是 **下划线转驼峰** 就可以绑定成功，所以就是 “driverClassName”

## 3.@ConditionalOnMissingBean注解

- @ConditionalOnBean // 当给定的在bean存在时,则实例化当前Bean

- @ConditionalOnMissingBean // 当给定的在bean不存在时,则实例化当前Bean

- @ConditionalOnClass // 当给定的类名在类路径上存在，则实例化当前Bean

- @ConditionalOnMissingClass // 当给定的类名在类路径上不存在，则实例化当前Bean

- @ConditionalOnExpression("'${mock.user.pin:null}' == 'null' || '${mock.user.pin}' == ''")

- **@ConditionalOnProperty**

  通过其三个属性prefix,name以及havingValue来实现的，其中prefix表示配置文件里节点前缀，name用来从application.properties中读取某个属性值，havingValue表示目标值。

  - 如果该值为空，则返回false;
  - 如果值不为空，则将该值与havingValue指定的值进行比较，如果一样则返回true;否则返回false。
  - 返回值为false，则该configuration不生效；为true则生效。

  下面代码演示为配置文件lind.redis.enable为true时才会注册RedisFactory这个bean

  ```java
  @Configuration
  @ConditionalOnProperty(prefix="lind.redis",name = "enable", havingValue = "true")
  public class RedisConfig {
    @Bean
    public RedisMap redisMap(){
      return new RedisMapImpl();
    }
  }
  ```

[springboot @ConditionalOnMissingBean注解的作用详解](https://www.jb51.net/article/193224.htm)

## 4. @**PropertySource**注解加载指定的属性文件

解析properities或者file文件。与ConfigurationProperties或者@value配合使用

```java
@ConfigurationProperties是类级别的注解，具体使用方式如下：
@Component
@ConfigurationProperties(prefix = "spring.datasource.shareniu")  
@PropertySource( name="jdbc-bainuo-dev.properties",value= {"classpath:config/jdbc-bainuo-dev.properties"},ignoreResourceNotFound=false,encoding="UTF-8")
public class CustomerDataSourceConfig1  {
    private  String url;
  }

```

需要注意的是PropertySourceFactory的加载时机早于Spring Beans容器，因此实现上不能依赖于Spring的IOC。

PropertySourceFactory要求实现类返回PropertySource。PropertySource是Spring属性（或者说配置）功能的核心接口，有很多实现，比如：

1. ResourcePropertySource 从Resource加载PropertySource

2. PropertiesPropertySource 从properties文件加载PropertySource

3. SystemEnvironmentPropertySource 从系统环境变量加载PropertySource

4. MapPropertySource 包装一个Map为PropertySource（Adapter模块）

5. CompositePropertySource 支持将若干PropertySource进行组合（Composite模式）

   ![image-20210921202836880](https://raw.githubusercontent.com/ly1246621281/PicGo/main/img/image-20210921202836880.png)

[Spring Boot自定义配置属性源（PropertySource）](https://www.jb51.net/article/141981.htm)

## 5.Spring 多线程



推荐分业务创建专享线程池。

自定义线程池

```java
@Configuration
@EnableAsync
public class ExecutorConfig {

    /** Set the ThreadPoolExecutor's core pool size. */
    private int corePoolSize = 10;
    /** Set the ThreadPoolExecutor's maximum pool size. */
    private int maxPoolSize = 200;
    /** Set the capacity for the ThreadPoolExecutor's BlockingQueue. */
    private int queueCapacity = 10;

    @Bean
    public TaskExecutorBuilder taskExecutorBuilder(){
        return null;
    }


    @Bean
    @Primary
    public Executor mySimpleAsync() {
        ThreadPoolTaskExecutor executor = new ThreadPoolTaskExecutor();
        executor.setCorePoolSize(corePoolSize);
        executor.setMaxPoolSize(maxPoolSize);
        executor.setQueueCapacity(queueCapacity);
        executor.setThreadNamePrefix("MySimpleExecutor-");
        executor.initialize();
        return executor;
    }

    @Bean
    public Executor myAsync() {
        ThreadPoolTaskExecutor executor = new ThreadPoolTaskExecutor();
        executor.setCorePoolSize(corePoolSize+20);
        executor.setMaxPoolSize(maxPoolSize);
        executor.setQueueCapacity(queueCapacity);
        executor.setThreadNamePrefix("MyExecutor-");

        // rejection-policy：当pool已经达到max size的时候，如何处理新任务
        // CALLER_RUNS：不在新线程中执行任务，而是有调用者所在的线程来执行
        executor.setRejectedExecutionHandler(new ThreadPoolExecutor.CallerRunsPolicy());
        executor.initialize();
        return executor;
    }
}
```



指定自定义线程池进行逻辑处理

```java
@Component
@Slf4j
public class AsyncTask {

    @Async("mySimpleAsync")
    public Future<String> doTask1() throws InterruptedException{
        log.info("Task1 started.");
        long start = System.currentTimeMillis();
        Thread.sleep(5000);
        long end = System.currentTimeMillis();

        log.info("Task1 finished, time elapsed: {} ms.", end-start);

        return new AsyncResult<>("Task1 accomplished!");
    }

    @Async("myAsync")
    public Future<String> doTask2() throws InterruptedException{
        log.info("Task2 started.");
        long start = System.currentTimeMillis();
        Thread.sleep(3000);
        long end = System.currentTimeMillis();

        log.info("Task2 finished, time elapsed: {} ms.", end-start);

        return new AsyncResult<>("Task2 accomplished!");
    }
}
```

springboot TaskExecutionAutoConfiguration中taskExecutorBuilder和 applicationTaskExecutor决定是否要使用默认线程池

```java
  @Lazy
    @Bean(
        name = {"applicationTaskExecutor", "taskExecutor"}
    )
    @ConditionalOnMissingBean({Executor.class}) //没有executor bean的时候使用默认的线程池
    public ThreadPoolTaskExecutor applicationTaskExecutor(TaskExecutorBuilder builder) {
        return builder.build();
    }
```

TaskExecutionProperties是线程池的默认配置项，队列数为无限可能导致内存溢出。

```java
 private int queueCapacity = 2147483647;
 private int coreSize = 8;
 private int maxSize = 2147483647;
 private boolean allowCoreThreadTimeout = true;
 private Duration keepAlive = Duration.ofSeconds(60L);
```

### 新提交一个任务时的处理流程：
1、如果线程池的当前大小还没有达到基本大小(poolSize < corePoolSize)，那么就新增加一个线程处理新提交的任务；

2、如果当前大小已经达到了基本大小，就将新提交的任务提交到阻塞队列排队，等候处理workQueue.offer(command)；

3、如果队列容量已达上限，并且当前大小poolSize没有达到maximumPoolSize，那么就新增线程来处理任务；

4、如果队列已满，并且当前线程数目也已经达到上限，那么意味着线程池的处理能力已经达到了极限，此时需要拒绝新增加的任务。至于如何拒绝处理新增的任务，取决于线程池的饱和策略RejectedExecutionHandler。  

接下来我们看下allowCoreThreadTimeOut和keepAliveTime属性的含义。在压力很大的情况下，线程池中的所有线程都在处理新提交的任务或者是在排队的任务，这个时候线程池处在忙碌状态。如果压力很小，那么可能很多线程池都处在空闲状态，这个时候为了节省系统资源，回收这些没有用的空闲线程，就必须提供一些超时机制，这也是线程池大小调节策略的一部分。通过corePoolSize和maximumPoolSize，控制如何新增线程；通过allowCoreThreadTimeOut和keepAliveTime，控制如何销毁线程。

原文链接：[ThreadPoolExecutor源码(一)线程池的corePoolSize、maximumPoolSize和poolSize](https://blog.csdn.net/aitangyong/article/details/38822505)

[使用样例](https://www.cnblogs.com/hsug/p/13303018.html)