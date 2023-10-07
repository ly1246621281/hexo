---
categories:
  - JAVA总结
  - JAVA（2020--2023）
---
Spring AOP切面失败

1.当前类调用类内方法

引申：

1）切面放在父类方法上，调用子类方法（子类方法内调用父类公共切面方法）--失效

![父类](https://img-blog.csdnimg.cn/20200602223102497.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3UwMTQ0Mzk2OTM=,size_16,color_FFFFFF,t_70#pic_left)

![子类](https://img-blog.csdnimg.cn/20200602223500760.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3UwMTQ0Mzk2OTM=,size_16,color_FFFFFF,t_70#pic_left)

[Spring AOP拦截抽象类(父类)中方法失效问题](https://blog.csdn.net/u014439693/article/details/106506177?spm=1001.2101.3001.6650.1&utm_medium=distribute.pc_relevant.none-task-blog-2%7Edefault%7ECTRLIST%7ERate-1-106506177-blog-126931618.235%5Ev36%5Epc_relevant_default_base3&depth_1-utm_source=distribute.pc_relevant.none-task-blog-2%7Edefault%7ECTRLIST%7ERate-1-106506177-blog-126931618.235%5Ev36%5Epc_relevant_default_base3&utm_relevant_index=2)

2）切面放在子类方法上，

父类为抽象类，子类实现抽象类的抽象方法。调用者调用抽象类方法，由抽象类找到子类实现。--失效

```java
@Slf4j
public abstract class AbstractQuartzJob {

    private static final String EXECUTE_METHOD_NAME = "execute";

    private AtomicBoolean running = new AtomicBoolean(false);

    @Resource(name = "simpleRedisDistributedLock")
    private DistributedLock simpleRedisDistributedLock;

    public void execute() {
    run();
    }

/**
 * 实际执行方法
 */
public abstract void run();
```

```java
@Slf4j
@Component("tryActivityInfoPullRefreshJob")
public class TryActivityInfoPullRefreshJob extends AbstractQuartzJob{
@Override
@ServiceCheck(switchName = "preMatchAndOrderSplitGraySwitchOn")
public void run() {
   //切面失效
}
```





[Spring Aop面试](https://www.bilibili.com/read/cv17702287/)