# Java 基础

## 1. 分析线程池的实现原理和线程的调度过程

### 1.1 什么时候使用线程池？

- 单个任务处理时间比较短
- 需要处理的任务数量很大

### 1.2 使用线程池的好处

引用自 [http://ifeve.com/java-threadpool/](http://ifeve.com/java-threadpool/) 的说明：

- **降低资源消耗** 。通过重复利用已创建的线程降低线程创建和销毁造成的消耗。
- **提高响应速度** 。当任务到达时，任务可以不需要的等到线程创建就能立即执行。
- **提高线程的可管理性** 。线程是稀缺资源，如果无限制的创建，不仅会消耗系统资源，还会降低系统的稳定性，使用线程池可以进行统一的分配，调优和监控。

### 1.3 ThreadPoolExecutor

![image](http://static.lovedata.net/jpg/2018/5/28/27abf026c12c631217100176ef0d7528.jpg)

![image](http://static.lovedata.net/jpg/2018/5/28/ec7340130d0fb9cf76247972c89e73eb.jpg)

参考

1. [探秘线程池 ThreadPoolExecutor 的任务调度过程 | 指间生活](http://www.zhenchao.org/2017/08/31/java-thread-pool-executor/)
2. [深入理解 Java 线程池：ThreadPoolExecutor - 后端 - 掘金](https://juejin.im/entry/58fada5d570c350058d3aaad)
3. [聊聊并发（三）Java线程池的分析和使用 | 并发编程网 – ifeve.com](http://ifeve.com/java-threadpool/)

## 2. 动态代理的几种方式

## 3. Spring AOP与IOC的实现

## 4. Dubbo的底层实现原理和机制，

## 5. 接口的幂等性的概念

1. **错误定义** ：幂等性是指重复使用 **同样的参数** 调用同一方法时总能获得 **同样的结果** 。比如对同一资源的GET请求访问结果都是一样的。
2. GET方法是向服务器查询，不会对系统产生副作用，具有幂等性（不代表每次请求都是相同的结果)
3. **HTTP的幂等性** 指的是一次和多次请求某一个资源应该具有相同的副作用。如通过PUT接口将数据的Status置为1，无论是第一次执行还是多次执行，获取到的结果应该是相同的，即执行完成之后Status =1。
4. 随着分布式系统及微服务的普及，因为网络原因而导致调用系统未能获取到确切的结果从而导致重试，这就需要被调用系统具有幂等性。
5. 定义： **幂等性是系统的接口对外一种承诺(而不是实现),** 承诺只要调用接口成功, 外部多次调用对系统的影响是一致的. 声明为幂等的接口会认为外部调用失败是常态, **并且失败之后必然会有重试.**
6. 解决办法
    1. 美团GTIS，通过Tair生成全局的业务id，在执行前和执行后进行判断
        1. ![image](http://static.lovedata.net/jpg/2018/5/28/4fbdeeef48fed51ed59db8109cbf494e.jpg)
7. 参考
    1. [分布式系统接口幂等性 - BruceFeng](https://blog.brucefeng.info/post/api-idempotent)
    2. [接口的幂等性 - 简书](https://www.jianshu.com/p/b09a2e9bcd29)
    3. [分布式系统互斥性与幂等性问题的分析与解决 -](https://tech.meituan.com/distributed-system-mutually-exclusive-idempotence-cerberus-gtis.html)

## 6. Synchronized和Lock的区别

[死磕Java并发：深入分析synchronized的实现原理](https://mp.weixin.qq.com/s/wHz0uL_LEe4OgLsSFGEZEg)

## 7. HashMap的原理和并发问题

### 7.1 原理

[HashMap实现原理分析 - CSDN博客](http://blog.csdn.net/vking_wang/article/details/14166593)

### 7.2 并发问题

## 8. 了解LinkedHashMap的应用吗

## 9. 线程池实现原理，Lock机制的实现

## 10. ConcurrentHashMap深入分析

### 10.1 concurrenthashmap怎么实习同步？各个版本的实现方案？

## 11. callable runnable 区别；

## 12. 进程线程区别；

## 13. hashMap和treeMap的区别，以及实现；

## 14. ByteBuffer的原理和使用

## 15. java的重载、重写、覆盖

![image](http://static.lovedata.net/jpg/2018/6/22/b1ac6f6922a609e910e0ed608f45a3dd.jpg)
![image](http://static.lovedata.net/jpg/2018/6/22/8484c892ff673e69d15a29646a020916.jpg)

方法的重写(Overriding)和重载(Overloading)是java多态性的不同表现，重写是父类与子类之间多态性的一种表现，重载可以理解成多态的具体表现形式。

### 15.1 重载

[Java 实例 – 方法重载 | 菜鸟教程](http://www.runoob.com/java/method-overloading.html)

重载(overloading) 是在一个类里面，方法名字相同，而参数不同。返回类型可以相同也可以不同。

每个重载的方法（或者构造函数）都必须有一个独一无二的参数类型列表。
最常用的地方就是构造器的重载。

如果有两个方法的方法名相同，但参数不一致，哪么可以说一个方法是另一个方法的重载。 具体说明如下：

- 方法名相同
- 方法的参数类型，参数个不一样
- 方法的返回类型可以不相同
- 方法的修饰符可以不相同
- main 方法也可以被重载

重载规则:

- 被重载的方法必须改变参数列表(参数个数或类型不一样)；
- 被重载的方法可以改变返回类型；
- 被重载的方法可以改变访问修饰符；
- 被重载的方法可以声明新的或更广的检查异常；
- 方法能够在同一个类中或者在一个子类中被重载。
- 无法以返回值类型作为重载函数的区分标准。

### 15.2 覆盖（重写）

重写是子类对父类的允许访问的方法的实现过程进行重新编写, 返回值和形参都不能改变。即外壳不变，核心重写！

重写方法不能抛出新的检查异常或者比被重写方法申明更加宽泛的异常   父类的一个方法申明了一个检查异常 IOException，但是在重写这个方法的时候不能抛出 Exception 异常，因为 Exception 是 IOException 的父类，只能抛出 IOException 的子类异常。

方法的重写规则

- 参数列表必须完全与被重写方法的 **相同；**
- 返回类型必须完全与被重写方法的返回类型 **相同；**
- 访问权限不能比父类中被重写的方法的访问权限更低。例如：如果父类的一个方法被声明为public，那么在子类中重写该方法就不能声明为protected。
- 父类的成员方法只能被它的子类重写。
- **声明为final的方法不能被重写。**
- **声明为static的方法不能被重写，但是能够被再次声明。**
- 子类和父类在同一个包中，那么子类可以重写父类所有方法，除了声明为private和final的方法。
- 子类和父类不在同一个包中，那么子类只能够重写父类的声明为public和protected的非final方法。
- 重写的方法能够抛出任何非强制异常，无论被重写的方法是否抛出异常。但是，重写的方法不能抛出新的强制性异常，或者比被重写方法声明的更广泛的强制性异常，反之则可以。
- 构造方法不能被重写。
- 如果不能继承一个方法，则不能重写这个方法。

[Java 重写(Override)与重载(Overload) | 菜鸟教程](http://www.runoob.com/java/java-override-overload.html)


## 16. Exception和Error有什么区别？

[欢迎回来](https://app.yinxiang.com/shard/s19/nl/3013102/138ba717-51ee-4052-abe3-eac3f4390ad0)

Exception 和 Error 都是继承了 Throwable 类，在 Java 中只有 Throwable 类型的实例才可以被抛出（throw）或者捕获（catch），它是异常处理机制的基本组成类型。

Exception 和 Error 体现了 Java 平台设计者对不同异常情况的分类。

Exception 是程序正常运行中，可以预料的意外情况，可能并且应该被捕获，进行相应处理。
Error 是指在正常情况下，不大可能出现的情况，绝大部分的 Error 都会导致程序（比如 JVM 自身）处于非正常的、不可恢复状态。既然是非正常情况，所以不便于也不需要捕获，常见的比如 OutOfMemoryError 之类，都是 Error 的子类。

Exception 又分为可检查（checked）异常和不检查（unchecked）异常，可检查异常在源代码里必须显式地进行捕获处理，这是编译期检查的一部分。
前面我介绍的不可查的 Error，是 Throwable 不是 Exception。
不检查异常就是所谓的运行时异常，类似 **NullPointerException、ArrayIndexOutOfBoundsException** 之类，通常是可以编码避免的逻辑错误，具体根据需要来判断是否需要捕获，并不会在编译期强制要求。

Checked Exception，因为这种类型设计的初衷更是为了从异常情况恢复，作为异常设计者，我们往往有充足信息进行分类

从性能角度来审视一下 Java 的异常处理机制，这里有两个可能会相对昂贵的地方：

try-catch 代码段会产生额外的性能开销，或者换个角度说，它往往会影响 JVM 对代码进行优化，所以建议仅捕获有必要的代码段，尽量不要一个大的 try 包住整段的代码；与此同时，利用异常控制代码流程，也不是一个好主意，远比我们通常意义上的条件语句（if/else、switch）要低效。

Java 每实例化一个 Exception，都会对当时的栈进行快照，这是一个相对比较重的操作。如果发生的非常频繁，这个开销可就不能被忽略了



![image](http://static.lovedata.net/jpg/2018/7/3/0b7956b3f218e27a2f07ac5c8c95d6d3.jpg)

## 17. NoClassDefFoundError 和 ClassNotFoundException 有什么区别

[关于NoClassDefFoundError和ClassNotFoundException异常 - Think Different - ITeye博客](http://wxl24life.iteye.com/blog/1919359)

[ClassNotFoundException和NoClassDefFoundError的区别 - 三郎 - 开源中国](https://my.oschina.net/jasonultimate/blog/166932)


