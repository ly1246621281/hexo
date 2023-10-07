---
categories:
  - JAVA总结
  - JAVA（2020--2023）
---
# SpringBoot获取属性

** spring boot 在多环境情况下我们需要根据不同的获取不一样的值， 我们会配置在不同的文件中,
那么我们怎么获取配置的属性值呢！ 下面介绍几种用法。** 

1. 除了默认配置在 application.properties 的多环境中添加属性：
   我们会在application.properties 中激活不同方式选择下面的不同文件进行发布。
   设置的激活参数：dev, test, prod
   spring.profiles.active=prod
   url.lm=editMessage
   url.orgCode=100120171116031838
   url.ybd=http://www.test.com/sales/
   url.PostUrl=/LmCpa/apply/applyInfo  

获取属性可以, 定义配置类：

@ConfigurationProperties(prefix = "url")    
public class  ManyEnvProperties{  
   private String lm;  
   private String orgCode;  
   private String ybd;  
   private String postUrl;  
   // 省列getter setter 方法  
}  
2. 使用之前在spring 中加载的value值形式

@Component  
public class ManyEnvProperties {  

   @Value("${url.lm}")  
   private String lmPage;  
   @Value("${url.ybd}")  
   private String sendYbdUrl;  
   @Value("${url.orgCode}")  
   private String orgCode;  
   @Value("${url.PostUrl}")  
   private String PostUrl;  
   // 省列getter setter 方法  
}  
3. 也可以使用spring boot 里面的 Environment 直接取值
显示注入， 其次是在需要的地方获取值

@Autowired  
private Environment env;  
logger.info("===============》 " + env.getProperty("url.lm"));  

4. 如果是自己新建的一个properties 文件：

@Component  
@ConfigurationProperties(prefix = "url")  
@PropertySource("classpath:/platform.properties")  
public class PropertiesEnv {  
   private String lm;  
   private String orgCode;  
   private String ybd;  
   private String postUrl;  

   // 省列getter setter 方法  
} 
————————————————
版权声明：本文为CSDN博主「zhongzunfa」的原创文章，遵循CC 4.0 BY-SA版权协议，转载请附上原文出处链接及本声明。
原文链接：https://blog.csdn.net/zhongzunfa/article/details/78644362



**propertySource: **

[[@PropertySource配置的用法与实现原理（十一）](https://www.cnblogs.com/h--d/p/14668695.html)](https://www.cnblogs.com/h--d/p/14668695.html)

> 理解是这个属性是解析XML或者Property文件使用。
>
> 如果加在启动文件上则可直接放置在spring容器里，加在实体类文件上也可以注册在Spring容器里。
>
> 与@Value @ConfigurationProperties配合使用，解析配置文件里的属性值，然后注入至容器内。