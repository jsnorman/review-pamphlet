# java多线程

## 1. lock 和 synchronize 的区别

### 用法
synchronized：在需要同步的对象中加入此控制，synchronized可以加在方法上，也可以加在特定代码块中，括号中表示需要锁的对象。
 
lock：需要显示指定起始位置和终止位置。一般使用ReentrantLock类做为锁，多个线程中必须要使用一个ReentrantLock类做为对象才能保证锁的生效。且在加锁和解锁处需要通过lock()和unlock()显示指出。所以一般会在finally块中写unlock()以防死锁。
 
### 区别
 
synchronized和lock性能区别

synchronized是托管给JVM执行的，而lock是java写的控制锁的代码。**在Java1.5中，synchronize是性能低效的**。因为这是一个重量级操作，需要调用操作接口，导致有可能加锁消耗的系统时间比加锁以外的操作还多。**相比之下使用Java提供的Lock对象，性能更高一些**。但是到了**Java1.6**，发生了变化。synchronize在语义上很清晰，可以进行很多优化，有适应自旋，锁消除，锁粗化，轻量级锁，偏向锁等等。**导致在Java1.6上synchronize的性能并不比Lock差**。官方也表示，他们也更支持synchronize

synchronize 原始是使用CPU悲观锁机制，独占所，独占所意味其他线程只能等待释放，阻塞导致上下文切换 效率很低

lock 是使用乐观锁，每次不加锁没有冲突去完成某项操作，如果因为冲突失败就充实  ，CAS compare and swap  。调用cpu特殊指令   ReentrantLock 获得锁的方法就是 compareAndSetState   是非阻塞算法。自动更新共享数据，检测到其他程序的干扰


### 应用区别

本身没什么区别，复杂同步应用中使用Lock，特别如下

1.某个线程在等待一个锁的控制权的这段时间需要中断
2.需要分开处理一些wait-notify，ReentrantLock里面的Condition应用，能够控制notify哪个线程
3.具有公平锁功能，每个到来的线程都将排队等候

 
 如果获取锁的线程需要等待I/O或者调用了sleep()方法被阻塞了，但仍持有锁，其他线程只能干巴巴的等着，这样就会很影响程序效率。
**因此就需要一种机制，可以不让等待的线程已知等待下去，比如值等待一段时间或响应中断，Lock锁就可以办到。**