### Inspire

>1.若程序报错返回，会导致重复消费MQ。
>
>2.多加日志，流程清晰。
>
>3.异常保护这块，或者说是方法间传递返回值异常判断这块，需要做到位。判断其返回值是否有其他情况，判断被调用方法抛出的异常是否都处理。
>
>son: renturn null;
>
>father:   List list = Arrays.asList(son());  //忘记判断list的返回值。



----

#### IDEA 配置Tomcat 几个点

1. Tomcat Deployment 处选择 explored 和war 包，一个是方便热部署文件形式发布，一个是war包形式

2. 工程的Project Structure处的Out directory处只是选择文件编译位置，配合Cataline_base使用

   若默认设置，则选择工程自己的Catalina_base

   ` Using CATALINA_BASE:   "C:\Users\zhangkai324\AppData\Local\JetBrains\IntelliJIdea2020.1\tomcat\Unnamed_upmp_2"`

   若设置输出至webapp，可同样使用tomcat的Catalina_base

3. 注意CATALINA_BASE 和CATALINA_HOME的区别，前者是工程自己配置目录，后者为Tomcat的配置目录

4. 工程的Project Structure的web资源配置很重要，注意放在/WEB-INF下面




## 上线记录

### 2020.10.26

1. JAR包冲突，导致日志输出失败

2. CPU暴涨，内存泄漏。FullGC频繁，7点PUSH量和每两分钟sms任务同时执行，JDBC连接池泄漏

3. 数据库连接配置修改， 导致数据库只读。

   **总结：**

   * 上线问题大概率是自身代码方面问题，找支持已只能是提供帮助。所以首先要自己思考上线改动点，问题点。

   * 缓存穿透问题未考虑清楚，会把压力都给数据库，造成数据库宕机。

   * 传统数据库一般一主两从，从库备份只读。监控时看CPU（慢查询等.）、磁盘繁忙（DDL等）、锋芒  
![img](D://zhangkai324//Documents//JD//office_dongdong//zhangkai324//Image//10a72286a6705bcd01fe6b3be139a924_src) 

## 2020.12.16 连接池，单例，每个方法有对应实例

