---
categories:
  - UPMP
---
# 数据库出库实时worker异常

定位日志：发现问题时间，确定影响范围。

解决问题：

1. 先保留错误数据，如果修复失败要回滚（毕竟线上数据）

2. 对仓维度统计正确的和 总量不正确的进行关联。（排查是否全部专仓，以及总条数等）

```sql
select a.activity_id,a.type as allt,b.type as dct,b.key as dcstore,a.`value` as allv,b.`value` as dcb from
(select * from activity_order_mid 
where create_time >= '2022-11-28 13:11' and create_time<'2022-11-28 13:16' and type=1
)a
left join 
(
select * from activity_order_mid 
where create_time >= '2022-11-28 13:11' and create_time<'2022-11-28 13:16' and type=2 and `key`='5-222'
)b 
on a.activity_id = b.activity_id
where a.`value` >b.`value`    
order by a.activity_id
```



3. 修复,可先选择一条测试。

   ```sql
   update activity_order_mid  a,(
   select * from activity_order_mid 
   where create_time >= '2022-11-28 13:11' and create_time<'2022-11-28 13:16' and type=2 and `key`='5-222'
   )b 
   set a.`value`=b.`value`,a.update_time=now()
   where  a.activity_id = b.activity_id and a.`value` >b.`value`  
   ```

   

明日定位报错原因：

```
[Upmp-Core-Web]2022-11-28 13:15:12.164 ERROR [JSF-BZ-22046-278-T-20] com.jd.y.upack.core.job.impl.UpmpJobOrderServiceImpl.orderRecordOutStockStatJob(UpmpJobOrderServiceImpl.java:327) - orderRecordOutStockStatJob订单出库量中间表统计异常
org.springframework.dao.DeadlockLoserDataAccessException:
### Error updating database. Cause: com.mysql.jdbc.exceptions.jdbc4.MySQLTransactionRollbackException: Deadlock found when trying to get lock; try restarting transaction
### The error may exist in file [/home/export/App/ysas.jd.com/WEB-INF/classes/mybatis/normal/ActivityOrderMidMapper.xml]
### The error may involve com.jd.y.upack.core.dao.normal.ActivityOrderMidDao.batchInsert-Inline
### The error occurred while setting parameters
### SQL: replace into activity_order_mid (activity_id, `type`, `key`, `value`, create_time, create_by, update_time, update_by, deleted ) VALUES ( ?, ?, ?, ?, ?, ?, now(), ?, 0 ) , ( ?, ?, ?, ?, ?, ?, now(), ?, 0 ) , ( ?, ?, ?, ?, ?, ?, now(), ?, 0 ) , ( ?, ?, ?, ?, ?, ?, now(), ?, 0 ) , ( ?, ?, ?, ?, ?, ?, now(), ?, 0 ) , ( ?, ?, ?, ?, ?, ?, now(), ?, 0 ) , ( ?, ?, ?, ?, ?, ?, now(), ?, 0 ) , ( ?, ?, ?, ?, ?, ?, now(), ?, 0 ) , ( ?, ?, ?, ?, ?, ?, now(), ?, 0 ) , ( ?, ?, ?, ?, ?, ?, now(), ?, 0 ) , ( ?, ?, ?, ?, ?, ?, now(), ?, 0 ) , ( ?, ?, ?, ?, ?, ?, now(), ?, 0 ) , ( ?, ?, ?, ?, ?, ?, now(), ?, 0 ) , ( ?, ?, ?, ?, ?, ?, now(), ?, 0 ) , ( ?, ?, ?, ?, ?, ?, now(), ?, 0 ) , ( ?, ?, ?, ?, ?, ?, now(), ?, 0 ) , ( ?, ?, ?, ?, ?, ?, now(), ?, 0 ) , ( ?, ?, ?, ?, ?, ?, now(), ?, 0 ) , ( ?, ?, ?, ?, ?, ?, now(), ?, 0 ) , ( ?, ?, ?, ?, ?, ?, now(), ?, 0 )
### Cause: com.mysql.jdbc.exceptions.jdbc4.MySQLTransactionRollbackException: Deadlock found when trying to get lock; try restarting transaction
; Deadlock found when trying to get lock; try restarting transaction; nested exception is com.mysql.jdbc.exceptions.jdbc4.MySQLTransactionRollbackException: Deadlock found when trying to get lock; try restarting transaction
at org.springframework.jdbc.support.SQLErrorCodeSQLExceptionTranslator.doTranslate(SQLErrorCodeSQLExceptionTranslator.java:271) ~[spring-jdbc-5.2.10.RELEASE.jar:5.2.10.RELEASE]
at org.springframework.jdbc.support.AbstractFallbackSQLExceptionTranslator.translate(AbstractFallbackSQLExceptionTranslator.java:72) ~[spring-jdbc-5.2.10.RELEASE.jar:5.2.10.RELEASE]
at org.mybatis.spring.MyBatisExceptionTranslator.translateExceptionIfPossible(MyBatisExceptionTranslator.java:91) ~[mybatis-spring-2.0.6.jar:2.0.6]
at org.mybatis.spring.SqlSessionTemplate$SqlSessionInterceptor.invoke(SqlSessionTemplate.java:441) ~[mybatis-spring-2.0.6.jar:2.0.6]
at com.sun.proxy.$Proxy113.insert(Unknown Source) ~[?:?]
at org.mybatis.spring.SqlSessionTemplate.insert(SqlSessionTemplate.java:272) ~[mybatis-spring-2.0.6.jar:2.0.6]
at org.apache.ibatis.binding.MapperMethod.execute(MapperMethod.java:62) ~[mybatis-3.5.6.jar:3.5.6]
at org.apache.ibatis.binding.MapperProxy$PlainMethodInvoker.invoke(MapperProxy.java:152) ~[mybatis-3.5.6.jar:3.5.6]
at org.apache.ibatis.binding.MapperProxy.invoke(MapperProxy.java:85) ~[mybatis-3.5.6.jar:3.5.6]
at com.sun.proxy.$Proxy173.batchInsert(Unknown Source) ~[?:?]
at com.jd.y.upack.core.job.impl.UpmpJobOrderServiceImpl.orderRecordOutStockStat(UpmpJobOrderServiceImpl.java:353) ~[classes/:1.0.0-SNAPSHOT]
at com.jd.y.upack.core.job.impl.UpmpJobOrderServiceImpl$$FastClassBySpringCGLIB$$58d7581c.invoke(<generated>) ~[classes/:1.0.0-SNAPSHOT]
at org.springframework.cglib.proxy.MethodProxy.invoke(MethodProxy.java:218) ~[spring-core-5.2.7.RELEASE.jar:5.2.7.RELEASE]
at org.springframework.aop.framework.CglibAopProxy$CglibMethodInvocation.invokeJoinpoint(CglibAopProxy.java:771) ~[spring-aop-5.2.7.RELEASE.jar:5.2.7.RELEASE]
at org.springframework.aop.framework.ReflectiveMethodInvocation.proceed(ReflectiveMethodInvocation.java:163) ~[spring-aop-5.2.7.RELEASE.jar:5.2.7.RELEASE]
at org.springframework.aop.framework.CglibAopProxy$CglibMethodInvocation.proceed(CglibAopProxy.java:749) ~[spring-aop-5.2.7.RELEASE.jar:5.2.7.RELEASE]
at org.springframework.aop.aspectj.MethodInvocationProceedingJoinPoint.proceed(MethodInvocationProceedingJoinPoint.java:88) ~[spring-aop-5.2.7.RELEASE.jar:5.2.7.RELEASE]
at com.jd.y.upack.core.aop.JprofileAopService.doJob(JprofileAopService.java:224) ~[classes/:1.0.0-SNAPSHOT]
at sun.reflect.GeneratedMethodAccessor463.invoke(Unknown Source) ~[?:?]
at sun.reflect.DelegatingMethodAccessorImpl.invoke(DelegatingMethodAccessorImpl.java:43) ~[?:1.8.0_261]
at java.lang.reflect.Method.invoke(Method.java:498) ~[?:1.8.0_261]
at org.springframework.aop.aspectj.AbstractAspectJAdvice.invokeAdviceMethodWithGivenArgs(AbstractAspectJAdvice.java:644) ~[spring-aop-5.2.7.RELEASE.jar:5.2.7.RELEASE]
at org.springframework.aop.aspectj.AbstractAspectJAdvice.invokeAdviceMethod(AbstractAspectJAdvice.java:633) ~[spring-aop-5.2.7.RELEASE.jar:5.2.7.RELEASE]
at org.springframework.aop.aspectj.AspectJAroundAdvice.invoke(AspectJAroundAdvice.java:70) ~[spring-aop-5.2.7.RELEASE.jar:5.2.7.RELEASE]
at org.springframework.aop.framework.ReflectiveMethodInvocation.proceed(ReflectiveMethodInvocation.java:186) ~[spring-aop-5.2.7.RELEASE.jar:5.2.7.RELEASE]
at org.springframework.aop.framework.CglibAopProxy$CglibMethodInvocation.proceed(CglibAopProxy.java:749) ~[spring-aop-5.2.7.RELEASE.jar:5.2.7.RELEASE]
at org.springframework.transaction.interceptor.TransactionAspectSupport.invokeWithinTransaction(TransactionAspectSupport.java:367) ~[spring-tx-5.2.7.RELEASE.jar:5.2.7.RELEASE]
at org.springframework.transaction.interceptor.TransactionInterceptor.invoke(TransactionInterceptor.java:118) ~[spring-tx-5.2.7.RELEASE.jar:5.2.7.RELEASE]
```

