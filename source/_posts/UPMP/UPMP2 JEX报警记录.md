---
categories:
  - UPMP
---
# UPMP2 JEX报警记录

1. null指针异常

   >  NullPointerException com.jd.y.upack.core.service.impl.OrderRecordJSFService in callOrderRecordJSF4Root
   >
   > 原因分析：brandId为null，商品主数据那边未维护品牌号

2. SendMail parse异常

   > 原因分析：界面填紧急联系邮箱为；。但保存未做强校验。

3. 索引越界

   > StringIndexOutOfBoundsException com.jd.y.upack.core.config.SendPayFilter in filter
   >
   > 原因分析：sendPay只有200位。20000000200000000000000002001200134030100000000001600000000001030000000000000000000110000000000101001010000000050000000000000000000000000000000000000000000000000000000000000000000000000100520300000010
   >
   > 订单号：

4. 出库消息

   > - com.jd.y.scc.sdk.mq.exception.**SccMessageException**: 订单[138830371974]消费订单出库JMQ失败
   >
   >   出库MessageSource为4，取消信息未到库房信息，因取消消息已接收并处理，导致数据库回滚失败，引发重试。
   >
   >   后续：需产品对接是否接信息未到库房消息。

5. d

