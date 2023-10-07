B-paas



## 2.实践

1. engine

   ```java
   private DomainFlowEngine orderSubmitFlowEngine;
   ```

2. flownode

   ```java
   @Resource
   private Map<String, OrderedDomainFlow> orderSubmitFlowMap;
   ```

   ```xml
   <bean id="orderSubmitFlowEngine" class="com.jd.matrix.core.domain.flow.engine.DomainFlowEngine">
       <constructor-arg name="engineCode" value="orderSubmitEngine"></constructor-arg>
       <constructor-arg name="engineName" value="订单-提单流程引擎"></constructor-arg>
       <constructor-arg name="domainCode" value="C2MDOMAIN.JDTRYDOMAIN.TRYORDER"></constructor-arg>
   </bean>
   <bean id="orderSubmitFlowMap" class="org.springframework.beans.factory.config.MapFactoryBean">
       <property name="sourceMap">
           <map>
               <entry key="2" value-ref="jiugongge"/>
               <entry key="5" value-ref="messageDelivery"/>
           </map>
       </property>
   </bean>
   
   <bean id="jiugongge" class="com.jd.matrix.core.domain.flow.OrderedDomainFlow">
       <constructor-arg name="code" value="jiugongge"></constructor-arg>
           <constructor-arg name="name" value="九宫格渠道"></constructor-arg>
           <constructor-arg name="domainFlowNodes">
               <list>
                   <ref bean="orderSubmitBeforeCheckNode"></ref>
                   <ref bean="orderSubmitQualificationCheckNode"></ref>
                   <ref bean="orderSubmitActivityInfoCheckNode"></ref>
                   <ref bean="orderSubmitAddressCheckNode"></ref>
                   <ref bean="orderSubmitCoreCheckNode"></ref>
                   <ref bean="orderSubmitAfterCheckNode"></ref>
               </list>
           </constructor-arg>
   </bean>
   ```

   ```java
   OutputMessage outputMessage = orderSubmitFlowEngine.start(activityPublishFlow, domainFlowContext);
   
     AbstractDomainFlow targetFlow = this.mergeFlow(flow, domainFlowContextContext);
           return targetFlow.enhanceCall(domainFlowContextContext.getInputMessage()); //调用DomainFlowNode.enhanceCall 然后就是 实现类去遍历node并按实现类策略调用Call方法。
   
   ```

   **实现类有顺序，并行以及管道（管道会将上游的出参给下游使用）。**

   ![image-20220420150109958](https://raw.githubusercontent.com/ly1246621281/PicGo/main/img/image-20220420150109958.png)

   ==Node怎么安排需要仔细考虑==

3. service 领域服务

4. ability 领域能力

5. ext  扩展点

6. vertical-biz  垂直身份

7. business-common 业务公共包

![image-20220420173259565](https://raw.githubusercontent.com/ly1246621281/PicGo/main/img/image-20220420173259565.png)

---

##  3.注意

1.打包 deploy到私服

```
<distributionManagement>
    <snapshotRepository>
        <id>snapshots</id>
        <name>JD maven2 repository-snapshots</name>
        <url>http://artifactory.jd.com/libs-snapshots-local</url>
    </snapshotRepository>
</distributionManagement>
```

2.维护版本号，注意jar 版本依赖问题

3.多moudle开发 ，优先引用的是当前moudle资源文件，但是上传代码避免流水线不通过，需上传Jar



## 4.其他

鸿宇好，想描述下现在进行paas化的提单功能，顺便请教几个问题。当前提单功能拆分扩展点为（地址校验，库存校验，提单入参核查，提单功能等），有两个垂直业务方。
问题1.两个垂直业务方都会校验地址、库存还有后续这些，目前这个相当于我们帮对方实现paas化，如果当做JSF接口做的话就是一个jsf接口。如果是这样的话是否是一个水平扩展点合适？这个水平扩展点不是很清楚。
问题2:查看藏经阁文档，对于里面讲的扩展点和场景关系是1对多： 场景是垂直业务身份下更细粒度的业务逻辑隔离标识，这个怎么理解
问题3：一个内部垂直业务包（通过maven引入）可实现多个业务身份逻辑，这个又是什么意思，在我这个提单功能是否可以利用呀？



1.公用的能力在common里，不同垂直业务身份调用可实现
2.

资质校验  1.用户是否有匹配上的记录   2. 用户是否在pin包
地址校验  1.专仓可配送  地址Id转换校验+
活动有效性校验 1.
+库存校验
提单参数
提单方法
后置

会议纪要：
1.用增表名修改
2.运维接口  jed技术支持 按分片建删除是否可行
3.本地缓存  主动被动区别
4.向业务要求用增消息投放 大促峰值匹配量调用量，落成文档---需业务认可
大促峰值  平时峰值  要求响应时长
给到品类新 大促峰值  平时峰值 响应时长
最大进行中活动数
5.接口设计限流 与上游协商
返回活动数设计成配置化
匹配多少次记录才命中全部品类新
6.用增推送策略，用于压测计算
7.压测效果不理想时，要强调自身能力。限流，降级方案（遍历活动数）
8.部署别名