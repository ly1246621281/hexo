---
categories:
  - JAVA总结
  - JAVA（2020--2023）
---
# Mysql Binlog介绍

0.[binlog介绍](https://baijiahao.baidu.com/s?id=1768973190518061520&wfr=spider&for=pc)

1.[mysql主從同步介紹](https://baijiahao.baidu.com/s?id=1741371045827915061&wfr=spider&for=pc)

* 使用binlog文件进行主从同步（sqlDump）

* 主从同步方式：同步、异步、半同步（一台slave机器同步成功即认为成功，有幻读情况--主挂时刻）、半同步优化（解决幻读）

2.binLake,jd--> Mysql（bin log发送mq通知）

[binLake介绍](https://cf.jd.com/pages/viewpage.action?pageId=213022010)

### 原理介绍

- 注册MySQL从库

  BinLake原理是模拟MySQL slave, dump增量的binlog, 并解析生成消息对象发往MQ的过程。

  以下是原理图:
  
  ![image-20230815163547197](https://raw.githubusercontent.com/ly1246621281/PicGo/main/img/image-20230815163547197.png)

- **dump****并解析****MySQL binlog****事件**
- ***\*封装发送消息\****
  封装业务方需要的数据成特定的格式, 目前支持{avro, protobuf} 格式, 将消息根据特定的顺序{实例级别顺序, 库顺序, 表顺序, 业务主键顺序 等} 发往MQ。