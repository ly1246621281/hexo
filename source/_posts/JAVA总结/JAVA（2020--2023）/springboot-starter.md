---
title: springboot starter
date: 20220803
categories:
  - JAVA总结
  - JAVA（2020--2023）
---

# 自定义Starter

## [实战自定义starter](https://www.jianshu.com/p/b5794bbd4b54?u_atoken=c955873d-977a-49c4-95b6-dd5c8fe584d2&u_asession=016W2NXtm9hDkb4V4OPWW2mDJTmVuFtk_BsdGMis6RwytwnmjQAGiRvjpcw9f3wdCJX0KNBwm7Lovlpxjd_P_q4JsKWYrT3W_NKPr8w6oU7K8y-6S-evvcPY-rvjgpWMbFPMqDvQo0pEVbhSjSW3HVJmBkFo3NEHBv0PZUm6pbxQU&u_asig=05ReNMps794XZLa2116BvqUYBjSGurixtQAfqoGSMeocyIvpYk3MUFd6HvojHursjHEYBl-Sc5p-N3rai4th5Rb87fDgsg4lzzzLxbz37fL9KJYUH0UGtnJeS_-F75KEpKZT93KnFRSsi-UT-lFuvoX44V7wnwJK5aFUp0aLQg6A39JS7q8ZD7Xtz2Ly-b0kmuyAKRFSVJkkdwVUnyHAIJzYfWdsfA9l6hNtc7tKB31jMpjFd6OTnJz6GCNoPSw6tWOF33MttdlYrQH7V14NMYIe3h9VXwMyh6PgyDIVSG1W_BOXPoTUeg3sBmzg2QZAtukJazJ-vHwBN39DhjYpKlM1rVYfg5X4Tr1Gnynru-A_BmA7f-t84zR_tn8bNJFNDumWspDxyAEEo4kbsryBKb9Q&u_aref=hw2DeVMd98nH9lFpliBVrnsiIdg%3D)

### 1.starter

> **官方命名空间**
>
> - 前缀：spring-boot-starter-
> - 模式：spring-boot-starter-模块名
> - 举例：spring-boot-starter-web、spring-boot-starter-jdbc
>
> **自定义命名空间**
>
> - 后缀：-spring-boot-starter
> - 模式：模块-spring-boot-starter
> - 举例：mybatis-spring-boot-starter

### 2.autoconfiguer

> ```undefined
> 启动器starter只是用来做依赖管理
> 需要专门写一个类似spring-boot-autoconfigure的配置模块
> 用的时候只需要引入启动器starter，就可以使用自动配置了
> ```

自动配置，解决了spring模块引入该maven之后还需要注入Spring容器，可直接使用@Autowired(需满足condition条件，或者配置文件-属性支持)

@ConditionOnXX  @ConfigurationProperties("")

注： starter只是引入该自动配置包，其实无任何作用。可直接引入autoconfigurtion包。

重点在spring.factories和自定义配置类：

```spring
# Auto Configure
org.springframework.boot.autoconfigure.EnableAutoConfiguration=\
com.example.hellospringbootstarterautoconfigurer.config.HelloServiceAutoConfiguration,\
com.example.hellospringbootstarterautoconfigurer.config.NewServiceAutoConfiguration
```

```java
@Configuration
public class NewServiceAutoConfiguration {
    @Bean(name = "autoConfigHelloTest")
    public HelloService helloService() {
        HelloService service = new HelloService();
        service.setName("ceshi");
        return service;
    }
}
```

## 补充：

### 1.spring.factories

Spring Boot中有一种非常[解耦](https://so.csdn.net/so/search?q=解耦&spm=1001.2101.3001.7020)的扩展机制：SpringFactories。这种扩展机制实际上是仿照Java中的SPI扩展机制来实现的。

SPI 的全名为 Service Provider Interface.大多数开发人员可能不熟悉，因为这个是针对厂商或者插件的。在java.util.ServiceLoader的文档里有比较详细的介绍。

简单的总结下 java SPI 机制的思想。我们系统里抽象的各个模块，往往有很多不同的实现方案，比如日志模块的方案，xml解析模块、jdbc模块的方案等。面向的对象的设计里，我们一般推荐模块之间基于接口编程，模块之间不对实现类进行硬编码。一旦代码里涉及具体的实现类，就违反了可拔插的原则，如果需要替换一种实现，就需要修改代码。为了实现在模块装配的时候能不在程序里动态指明，这就需要一种服务发现机制。

java SPI 就是提供这样的一个机制：==为某个接口寻找服务实现的机制==。有点类似IOC的思想，就是将装配的控制权移到程序之外，在模块化设计中这个机制尤其重要。


### spring的spi机制

在Spring中也有一种类似与Java SPI的加载机制。 它在`META-INF/spring.factories`文件中配置接口的实现类名称，然后在程序中读取这些配置文件并实例化。 这种自定义的SPI机制是Spring Boot Starter实现的基础。

![img](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91c2VyLWdvbGQtY2RuLnhpdHUuaW8vMjAxOS84LzUvMTZjNjA4MjIyN2RmNDlmNA?x-oss-process=image/format,png)

在日常工作中，我们可能需要实现一些SDK或者Spring Boot Starter给被人使用时， 我们就可以使用Factories机制。Factories机制可以让SDK或者Starter的使用只需要很少或者不需要进行配置，只需要在服务中引入我们的jar包即可。

### 2.springboot 2.7版本

果你是Spring Boot用户的话，一定有这样的开发体验，当我们要引入某个功能的时候，只需要在maven或gradle的配置中直接引入对应的Starter，马上就可以使用了，而不需要像传统Spring应用那样写个xml或java配置类来初始化各种Bean。

如果你有探索过这些Starter的原理，那你一定知道Spring Boot并没有消灭这些原本你要配置的Bean，而是将这些Bean做成了一些默认的配置类，同时利用/META-INF/spring.factories这个文件来指定要加载的默认配置。

这样当Spring Boot应用启动的时候，就会根据引入的各种Starter中的/META-INF/spring.factories文件所指定的配置类去加载Bean。

而这次刚发布的Spring Boot 2.7中，有一个不推荐使用的内容就是关于这个/META-INF/spring.factories文件的，所以对于有自定义Starter的开发者来说，有时间要抓紧把这一变化改起来了，因为在Spring Boot 3开始将移除对/META-INF/spring.factories的支持。



那么具体怎么改呢？下面以之前我们编写的一个swagger的starter为例，它的/META-INF/spring.factories内容是这样的：

org.springframework.boot.autoconfigure.EnableAutoConfiguration=com.spring4all.swagger.SwaggerAutoConfiguration

我们只需要创建一个新的文件：/META-INF/spring/org.springframework.boot.autoconfigure.AutoConfiguration.imports，内容的话只需要直接放配置类就可以了，比如这样：

com.spring4all.swagger.SwaggerAutoConfiguration
注意：这里多了一级spring目录。
————————————————
版权声明：本文为CSDN博主「程序猿DD」的原创文章，遵循CC 4.0 BY-SA版权协议，转载请附上原文出处链接及本声明。
原文链接：https://blog.csdn.net/dyc87112/article/details/124969130

**新版本如何做到新老注册方式同时兼容？**
SpringFactoriesLoader用来加载spring.factories配置类
ImportCandidates用来加载META-INF/spring/org.springframework.boot.autoconfigure.AutoConfiguration.imports配置文件；
看下其中一种场景AutoConfigurationExcludeFilter类兼容两种方案的源码：

	protected List<String> getAutoConfigurations() {
		if (this.autoConfigurations == null) {
			List<String> autoConfigurations = new ArrayList<>(
					SpringFactoriesLoader.loadFactoryNames(EnableAutoConfiguration.class, this.beanClassLoader));
			ImportCandidates.load(AutoConfiguration.class, this.beanClassLoader).forEach(autoConfigurations::add);
			this.autoConfigurations = autoConfigurations;
		}
		return this.autoConfigurations;
	}


### 解决 Spring Boot 中不能被默认路径扫描的配置类的方式，有 2 种：

> （1）在 Spring Boot 主类上使用 @Import 注解
> （2）使用 spring.factories 文件

spring-core 包里定义了 SpringFactoriesLoader 类，这个类实现了检索 META-INF/spring.factories 文件，并获取指定接口的配置的功能。在这个类中定义了两个对外的方法：

loadFactories 根据接口类获取其实现类的实例，这个方法返回的是对象列表。
loadFactoryNames 根据接口获取其接口类的名称，这个方法返回的是类名的列表。

，在这个方法中会遍历整个 spring-boot 项目的 classpath 下 ClassLoader 中所有 jar 包下的 spring.factories文件。也就是说我们可以在自己的 jar 中配置 spring.factories 文件，不会影响到其它地方的配置，也不会被别人的配置覆盖。


```java
Enumeration<URL> urls = classLoader.getResources(FACTORIES_RESOURCE_LOCATION);
while (urls.hasMoreElements()) {
   URL url = urls.nextElement();
   UrlResource resource = new UrlResource(url);
会加载类路径下的所有spring.factories
```

