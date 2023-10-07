---
title: 线程池学习
date： 20201223
tag： JAVA
---

# ThreadPool

[线程池之ThreadPoolExecutor使用](https://www.jianshu.com/p/f030aa5d7a28)

[线程池的优雅关闭实践](https://www.jianshu.com/p/bdf06e2c1541)

**线程池自动关闭的两个条件：1、线程池的引用不可达；2、线程池中没有线程；**

> 对于2 coreThreadSize 为0，newFix‘edThreadPool是不会存在自动关闭线程池。newCachedThreadPool可能会，但建议手动关闭



**最佳实践总结**



- 【强制】使用ThreadPoolExecutor的构造函数声明线程池，避免使用Executors类的newFixedThreadPool和newCachedThreadPool。



- 【强制】 创建线程或线程池时请指定有意义的线程名称，方便出错时回溯。即threadFactory参数要构造好。



- 【建议】建议不同类别的业务用不同的线程池。



- 【建议】CPU密集型任务(N+1)：这种任务消耗的主要是CPU资源，可以将线程数设置为N（CPU核心数）+1，比CPU核心数多出来的一个线程是为了防止线程偶发的缺页中断，或者其它原因导致的任务暂停而带来的影响。一旦任务暂停，CPU就会处于空闲状态，而在这种情况下多出来的一个线程就可以充分利用CPU的空闲时间。



- 【建议】I/O密集型任务(2N)：这种任务应用起来，系统会用大部分的时间来处理I/O交互，而线程在处理I/O的时间段内不会占用CPU来处理，这时就可以将CPU交出给其它线程使用。因此在I/O密集型任务的应用中，我们可以多配置一些线程，具体的计算方法是2N。



- 【建议】workQueue不要使用无界队列，尽量使用有界队列。避免大量任务等待，造成OOM。



- 【建议】如果是资源紧张的应用，使用allowsCoreThreadTimeOut可以提高资源利用率。



- 【建议】虽然使用线程池有多种异常处理的方式，但在任务代码中，使用try-catch最通用，也能给不同任务的异常处理做精细化。



- 【建议】对于资源紧张的应用，如果担心线程池资源使用不当，可以利用ThreadPoolExecutor的API实现简单的监控，然后进行分析和优化。



![图片](https://mmbiz.qpic.cn/mmbiz_png/Z6bicxIx5naJ90Ks7UHT87Q3foSu1rY6y8e4XGCibgibia2R8iaYGogDHTAEVwb3O9eOUBuWMRhyL2iaNbx5ZvzplIbQ/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)



线程池初始化示例：

```
private static final ThreadPoolExecutor pool;
static {        
ThreadFactory threadFactory = new ThreadFactoryBuilder().setNameFormat("po-detail-pool-%d").build();        
pool = new ThreadPoolExecutor(4, 8, 60L, TimeUnit.MILLISECONDS, new LinkedBlockingQueue<>(512),            threadFactory, new ThreadPoolExecutor.AbortPolicy());        
pool.allowCoreThreadTimeOut(true);   
} 
```

- threadFactory：给出带业务语义的线程命名。

- corePoolSize：快速启动4个线程处理该业务，是足够的。

- maximumPoolSize：IO密集型业务，我的服务器是4C8G的，所以4*2=8。

- keepAliveTime：服务器资源紧张，让空闲的线程快速释放。

- pool.allowCoreThreadTimeOut(true)：也是为了在可以的时候，让线程释放，释放资源。

- workQueue：一个任务的执行时长在100~300ms，业务高峰期8个线程，按照10s超时（已经很高了）。10s钟，8个线程，可以处理10 * 1000ms / 200ms * 8 = 400个任务左右，往上再取一点，512已经很多了。

- handler：极端情况下，一些任务只能丢弃，保护服务端。