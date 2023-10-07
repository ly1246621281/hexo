---
title: Spring学习
date: 2020.12.24
tag: JAVA
categories:
  - JAVA总结
  - JAVA（2020--2023）
---

# 	Spring

## 1.注入有参构造方法

1.通过注解方式注入有参的构造函数

![img](https://img2018.cnblogs.com/blog/1063429/201811/1063429-20181103161623025-676105185.png)

把@Autowired注解放在构造函数上方，在构造函数里写上需要注入的形参即可

 2.通过XML配置文件方式定义有参构造函数

![img](https://img2018.cnblogs.com/blog/1063429/201811/1063429-20181103161924265-292142024.png)

> 此时有以下三种方法来完成有参数的类的实例化。
>
> 第一种：根据参数的顺序索引来注入参数
>
> ```java
> #ApplicationContext.xml配置
> <bean id="computer" class="cn.kermit.test.Computer" >
>     <constructor-arg index="0" value="IBM"></constructor-arg>
>     <constructor-arg index="0" value="17寸"></constructor-arg>
> </bean>
> ```
>
> 第二种：使用类型来注入,type是参数的类型。但这种方法在有两个相同类型的参数时就不能用了。
>
> ```java
> <bean id="computer" class="cn.kermit.test.Computer" >
>     <constructor-arg type="java.lang.String" value="IBM" />
>     <constructor-arg type="int" value="17" />
> </bean>
> ```
>
> 第三种：使用name指定参数名再赋值，这个方法最实用
>
> ```java
> <bean id="computer" class="cn.kermit.test.Computer" >
>     <constructor-arg name="brank" value="IBM" />
>     <constructor-arg name="size" value="17" />
> </bean>
> 
> #运行结果：
> #Computer [brank=IBM品牌, Size=17]
> ```

**注：**

*类对象注入在类加载时候完成，对于想使用有参构造方法生成多实例，此注入方式不行，仍需将注入对象get/set*



## 2.对象注入静态类

封装工具类，并通过@Component注解成功能组件，但是功能组件中的方法一般都是静态方法，静态方法只能调用静态成员变量，于是就有了下面的问题。封有的时候封装功能组件会需要底层的service注入，怎么办呢？
去网上搜了下解决办法，简单总结一下几种实现方式；

> 1.xml方式实现；
>
> <bean id="mongoFileOperationUtil" class="com.*.*.MongoFileOperationUtil" init-method="init">
> <property name="dsForRW" ref="dsForRW"/>
> </bean>
>
> ```java
> public class MongoFileOperationUtil {
> 
> private static AdvancedDatastore dsForRW;
> 
> private static MongoFileOperationUtil mongoFileOperationUtil;
> 
> public void init() {
> mongoFileOperationUtil = this;
> mongoFileOperationUtil.dsForRW = this.dsForRW;
> }
> 
> }
> ```
>
> 这种方式适合基于XML配置的WEB项目；
>
>
> 2.@PostConstruct方式实现；
>
> ```java
> import org.mongodb.morphia.AdvancedDatastore;
> import org.springframework.beans.factory.annotation.Autowired;
> 
> 
> @Component
> public class MongoFileOperationUtil {
> @Autowired
> private static AdvancedDatastore dsForRW;
> 
> private static MongoFileOperationUtil mongoFileOperationUtil;
> 
> @PostConstruct
> public void init() {
> mongoFileOperationUtil = this;
> mongoFileOperationUtil.dsForRW = this.dsForRW;
> }
> 
> }
> ```
>
> @PostConstruct 注解的方法在加载类的构造函数之后执行，也就是在加载了构造函数之后，执行init方法；(@PreDestroy 注解定义容器销毁之前的所做的操作)
> 这种方式和在xml中配置 init-method和 destory-method方法差不多，定义spring 容器在初始化bean 和容器销毁之前的所做的操作；
>
> 3.set方法上添加@Autowired注解，类定义上添加
>
> ```java
> @Component注解；
> 
> import org.mongodb.morphia.AdvancedDatastore;
> import org.springframework.beans.factory.annotation.Autowired;
> import org.springframework.stereotype.Component;
> 
> 
> @Component
> public class MongoFileOperationUtil {
> 
> private static AdvancedDatastore dsForRW;
> 
> @Autowired
> public void setDatastore(AdvancedDatastore dsForRW) {
> MongoFileOperationUtil.dsForRW = dsForRW;
> }
> }
> ```
>
> 首先Spring要能扫描到AdvancedDatastore的bean，然后通过setter方法注入；
>
> 然后注意：成员变量上不需要再添加@Autowired注解；
>
> **4. @Autowired 用在构造函数上**
> 我们知道@Autowired 注释，可以对类成员变量、方法及构造函数进行标注，完成自动装配的工作，此种方式就是在构造函数上使用@Autowired。
> 代码参考：
>
> ------
>
> @Component
> public class ScriptExecuteContent {
>
> ```java
> private static SignRepository signRepository;
> 
> @Autowired
> public ScriptExecuteContent(SignRepository signRepository) {
>     ScriptExecuteContent.signRepository = signRepository;
> }
> 
> public static String checkSign(String certNo, String acctNo, String instCode) {
>     Sign sign = signRepository.findByCertNoAndAcctNoAndInstCode(certNo, acctNo, instCode);
>     if (null != sign
>             && StringUtils.equals(sign.getStatus(), StatusEnum.SUCCESS.code())
>             && DateUtil.getCurrentDate().before(sign.getExpireTime())) {
>         return "1";
>     } else {
>         return "0";
>     }
> }
> ```
>
> }

