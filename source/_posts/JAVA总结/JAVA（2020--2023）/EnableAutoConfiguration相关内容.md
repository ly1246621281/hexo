---
categories:
  - JAVA总结
  - JAVA（2020--2023）
---
# EnableAutoConfiguration相关内容

## 1.设计自动配置Jar，提供自动配置

springboot 项目，引入外部sdk后，会自动将配置类扫描放入spring容器，并加载配置文件的配置。原因在于提供sdk的 spring.factories全路径：../src/main/resources/META-INF/spring.factories。

```properties
# Auto Configure
org.springframework.boot.autoconfigure.EnableAutoConfiguration=\
com.jd.y.scc.sdk.mq.autoconfig.JmqClientAutoConfiguration
```

[使用spring.factories加载类和@EnableAutoConfiguration注解](https://blog.csdn.net/zhangbeizhen18/article/details/129740389)

## 2.注解理解

EnableAutoConfiguration注解的功能大概如下。

开启Spring应该上下文的自动配置，尽可能去加载需要的配置Beans。自动配置类，一般是根据你的classpath路径来应用的。
当你使用了SpringBootApplication注解，在context中自动加载配置就已经开启，不需要其他操作。
可以通过exclude()传入Class类型；excludeName() 传入名称来排除其他类型的加载。自动加载的配置始终是在普通用户定义的Bean加载之后执行。
默认EnableAutoConfiguration扫描的是当前启用注解的Class对象的目录作为root目录。所以这个目录下的所有子目录都会被扫描。
自动装载的配置类，也是常规的SpringBean(被@Configuration修饰，@Configuration则被@Component修饰（如果你写的@Configuration在启动类的包名下，开启注解扫描的情况下，也是会把@Configuration注册为Bean对象。）)。当然也可以通过SpringFactoriesLoader的机制来定位到对应的org.springframework.boot.autoconfigure.EnableAutoConfiguration类(不在同用户扫描包目录中)。
一般配合Conditional、ConditionalOnClass、ConditionalOnMissingBean一起使用。
————————————————
版权声明：本文为CSDN博主「石奈子」的原创文章，遵循CC 4.0 BY-SA版权协议，转载请附上原文出处链接及本声明。
原文链接：https://blog.csdn.net/u014042066/article/details/107134281/

## 3.可能发生问题

1. [使用@Component会导致spring.factories中的EnableAutoConfiguration无效](https://blog.csdn.net/u013202238/article/details/122038257)

2. 自动配置类的执行顺序问题(https://blog.csdn.net/u013202238/article/details/123360141)

## 4. 其他

**2.1 @Configuration注解可以让@Bean注解对象\**依赖于当前配置类的其它Bean。\****

[@Configuration和@[Component](https://so.csdn.net/so/search?q=Component&spm=1001.2101.3001.7020)注解的区别](https://blog.csdn.net/yldmkx/article/details/117780008?spm=1001.2101.3001.6650.1&utm_medium=distribute.pc_relevant.none-task-blog-2%7Edefault%7ECTRLIST%7ERate-1-117780008-blog-122077537.235%5Ev36%5Epc_relevant_default_base3&depth_1-utm_source=distribute.pc_relevant.none-task-blog-2%7Edefault%7ECTRLIST%7ERate-1-117780008-blog-122077537.235%5Ev36%5Epc_relevant_default_base3&utm_relevant_index=2)