---
categories:
  - JAVA总结
  - JAVA（2020--2023）
---
# 应用方法TP99飙升，Mysql行锁问题分析

## 1. reduce扣减库存问题

  应用方法监控：![img](https://raw.githubusercontent.com/ly1246621281/PicGo/main/img/1636097533477_src) 

  数据库DB当时QPS：![img](https://raw.githubusercontent.com/ly1246621281/PicGo/main/img/JdOnline20211105152944.png) 

 数据库CPU监控图： ![img](https://raw.githubusercontent.com/ly1246621281/PicGo/main/img/JdOnline20211105153009.png) 

跟进DBA和架构师了解到：

1.**慢SQL是诱因** 

> 慢SQL会创建/释放连接，占用磁盘IO，内存，缓存Buff等操作争抢数据库资源，使得整体库的性能下降，导致普通DML语句受影响

2.并发度问题

> **order_record**表有锁的问题，因为order_id是普通索引，不论是查询、更新、插入都会有间隙锁生成，锁定普通索引+主键索引。
>
> **warehouse_dist**表不大，并发度高，没有插入业务，只有更新业务，所以间隙锁暂无影响。



## 2.行锁问题分析

![img](https://upload-images.jianshu.io/upload_images/3921795-a696cd4bdf198375.jpg?imageMogr2/auto-orient/strip|imageView2/2/w/1080/format/webp)

### 行锁和读锁问题

![img](https://upload-images.jianshu.io/upload_images/3921795-c40157afd6da1f49.jpg?imageMogr2/auto-orient/strip|imageView2/2/w/1200/format/webp)

### 隔离级别

> 1.read uncommitted
>
> 可以看到未提交的数据（脏读），举个例子：别人说的话你都相信了，但是可能他只是说说，并不实际做。
>
> 2.read committed
>
> 读取提交的数据。但是，可能多次读取的数据结果不一致（不可重复读，幻读）。用读写的观点就是：读取的行数据，可以写。
>
> 3.repeatable read(MySQL默认隔离级别)
>
> 可以重复读取，但有幻读。读写观点：**读取的数据行不可写，但是可以往表中新增数据**。在MySQL中，其他事务新增的数据，看不到，不会产生幻读。采用多版本并发控制（MVCC）机制解决幻读问题。
>
> 4.serializable
>
> 可读，不可写。像java中的锁，写数据必须等待另一个事务结束。

![img](https://raw.githubusercontent.com/ly1246621281/PicGo/main/img/3921795-c99ba6a40405fd62.jpg)

### 解决幻读问题：

> 快照读： MVCC解决
>
> 当前读： 行锁+gap锁解决

那么Next-Key Lock是什么？怎么解决的幻读？

行锁有写锁X和读锁S两种，实际上行锁有3种实现算法，Next-Key Lock是其中之一。

第一种叫做**Record Lock**，字面意思，行记录的锁，实际上指的是对索引记录的锁定。

比如执行语句`select * from user where age=10 for update`，将会锁住`user`表所有`age=10`的行记录，所有对`age=10`的记录的操作都会被阻塞。

第二种都比较熟悉，叫做**Gap Lock**，也就是**间隙锁**，它用于锁定的索引之间的间隙，但是不会包含记录本身。

比如语句`select * from user where age>1 and age<10 for update`，将会锁住`age`在(1,10)的范围区间，此时其他事务对该区间的操作都会被阻塞。

间隙锁是可重复读RR隔离级别下特有的，另外还有几种场景也会不使用间隙锁。

1. 事务隔离级别设置为不可重复读RC ，这样肯定没有间隙锁了。
2. `Innodb_locks_unsafe_for_binlog`设置为1
3. 另外一种情况适用于**主键索引或者唯一索引**的等值查询条件，比如`select * from user where id=1`，`id`是主键索引，这样只使用Record Lock就可以了，因为能唯一锁定一条记录，所以没有必要再加间隙锁了，这是锁降级的过程。

而第三种**Next-Key Lock**实际上就是相当于Record Lock+Gap Lock的组合。比如索引有10，20，30几个值，那么被锁住的区间可能会是(-∞,10]，(10,20]，(20,30]，(30,+∞)。



作者：艾小仙人
链接：https://www.jianshu.com/p/e065b295cdb6
来源：简书
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。



[拿捏！隔离级别、幻读、Gap Lock、Next-Key Lock](https://www.jianshu.com/p/e065b295cdb6)

## 事务使用注意事项

### 最后给出事务使用的几点建议

- 控制事务大小，减少锁定的资源量和锁定时间长度。
- 所有的数据检索都通过索引来完成，从而避免因为无法通过索引加锁而升级为表锁。
- 减少基于范围的数据检索过滤条件，避免因为间隙锁带来的负面影响而锁定了不该锁定的数据。
- 在业务条件允许下，尽量使用较低隔离级别的事务隔离。减少隔离级别带来的附加成本。
- 合理使用索引，让innodb在索引上面加锁的时候更加准确。
- 在应用中尽可能做到访问的顺序执行。
- 如果容易死锁，就可以考虑使用表锁来减少死锁的概率



MySQL InnoDB默认[行级锁](http://www.hollischuang.com/archives/914)。行级锁都是基于索引的

行级锁变为表级锁情况如下：

1、如果一条SQL语句用不到索引是不会使用行级锁的，会使用表级锁把整张表锁住。

2、表字段进行变更。

3、进行整表查询。(没使用索引)

4、like语句查询的时候。(没使用索引)

