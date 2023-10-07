---
title: f
categories:
  - JAVA总结
  - JAVA（2020--2023）
date:
---

# Log4J 

## 1.耗时项

1. 格式化

   ```
   logger.debug("Entry number: {} is {}", i, entry[i]);
   修改为：
   if(log.isDebugEnabled){}
   ```

2. 记录方法名，行号等信息

   > *捕获调用方位置对所有日志库都有类似的影响，并且会减慢异步日志记录的速度大约 30-100 倍*。

## 2.对比

* 从同步文件记录-吞吐量-响应时长对比

log4j2> log4j1 >logback

https://logging.apache.org/log4j/2.x/performance.html

* 针对log4j2的Appender来讲

  > 备选：File、RandomAccessFile 和 MemoryMappedFile 
  >
  > 性能最佳：RandomAccessFile 和 MemoryMappedFile 
  >
  > MemoryMappedFile 无滚动变体
  >
  > ---事实证明，与 Log4j 2.5 相比，2.6 中的无垃圾文本编码逻辑为这些附加程序提供了性能提升。过去 RandomAccessFile appender 明显更快，尤其是在多线程场景中，但随着 2.6 版本的发布，File appender 性能有所提高，这两个 appender 之间的性能差异更小。

## 3.异步日志

异步日志记录可以通过在单独的线程中执行 I/O 操作来提高应用程序的性能，同步线程格式化，记录输出样式，异步线程去做IO。

1.使用方式

*Log4j-2.9 和更高版本需要在类路径上使用disruptor-3.3.4.jar 或更高版本。在 Log4j-2.9 之前，需要disruptor-3.0.0.jar 或更高版本。*

> 这是最简单的配置并提供最佳性能。要使所有记录器异步，请将中断器 jar 添加到类路径并将系统属性设置`log4j2.contextSelector` 为`org.apache.logging.log4j.core.async.AsyncLoggerContextSelector`or `org.apache.logging.log4j.core.async.BasicAsyncLoggerContextSelector`。

2.注意事项

`includeLocation="true"` 会影响性能，但是其可以将日志方法名、行号等信息传递给IO线程。

对于异步日志来说，使用位置进行记录比不使用位置慢 30-100 倍。

3.异步日志记录性能

在 64 线程的测试中，异步 logger 比异步 appender 快 12 倍，比同步 logger 快 68 倍。

对于异步追加器，当添加更多线程时，所有线程的总日志记录吞吐量大致保持不变。异步记录器在多线程场景中更有效地利用机器上可用的多个内核。

![异步记录器具有最高的吞吐量。](https://logging.apache.org/log4j/2.x/images/async-throughput-comparison.png)

https://logging.apache.org/log4j/2.x/manual/async.html