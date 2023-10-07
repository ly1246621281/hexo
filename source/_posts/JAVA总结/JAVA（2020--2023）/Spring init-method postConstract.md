---
categories:
  - JAVA总结
  - JAVA（2020--2023）
---
# Spring init-method postConstract

### 

 	1.spring bean的初始化执行顺序：构造方法---> @PostConstruct注解的方法---> afterPropertiesSet方法 ---> init-method指定的方法。具体可以参考例子

2. afterPropertiesSet通过接口实现方式调用进行赋值操作（效率上高一点），@PostConstruct和init-method都是通过反射机制调用。
   ————————————————
   版权声明：本文为CSDN博主「奈文杰」的原创文章，遵循CC 4.0 BY-SA版权协议，转载请附上原文出处链接及本声明。
   原文链接：https://blog.csdn.net/Hj95815/article/details/117779474

3. **首先这些过程都在很重要的抽象类AbstractAutowireCapableBeanFactory抽象类中 这个类就是常用的DefaultListableBeanFactory的父类**

   **1.spring先根据beanDefinition创建出了bean 的实例**

    

   **2.执行了populateBean方法 把属性和依赖都注入了**  

    

   **3.执行了 initializeBean(beanName, exposedObject, mbd);方法 这里面才进行了Aware相关方法，afterPropertiesSet 和 initMethod 方法的调用**

   **可见这3种方法调用又都是在bean实例已经创建好，且属性值和依赖的其他bean实例都已经注入以后 才得到调用的**

    

   **4.后面的代码可以看出 Aware相关方法最先执行，afterPropertiesSet 第二执行  ，initMethod 方法最后执行**

    

   **另外多说一句 afterPropertiesSet方法是很有用的，比如 AOP事务管理用到的类，TransactionProxyFactoryBean 就是利用afterPropertiesSet方法事先把事务管理器**

   **TransactionManager的代理类对象给生成好了，后面调用FactoryBean对象的getObject方法的时候，就直接把这个代理对象返回出去了。**

[[initMethod 和 afterPropertiesSet 以及 AwareMethod方法的执行时机](https://www.cnblogs.com/wz1989/p/4273480.html)](https://www.cnblogs.com/wz1989/p/4273480.html)

