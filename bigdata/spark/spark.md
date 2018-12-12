

#  基础知识

## 1. Spark性能优化主要有哪些手段？

1、常规性能调优：分配资源、并行度。。。等

2、JVM调优（Java虚拟机）：JVM相关的参数，通常情况下，如果你的硬件配置、基础的JVM的配置，都ok的话，JVM通常不会造成太严重的性能问题；反而更多的是，在troubleshooting中，JVM占了很重要的地位；JVM造成线上的spark作业的运行报错，甚至失败（比如OOM）。

3、shuffle调优（相当重要）：spark在执行groupByKey、reduceByKey等操作时的，shuffle环节的调优。这个很重要。shuffle调优，其实对spark作业的性能的影响，是相当之高！！！经验：在spark作业的运行过程中，只要一牵扯到有shuffle的操作，基本上shuffle操作的性能消耗，要占到整个spark作业的50%~90%。10%用来运行map等操作，90%耗费在两个shuffle操作。groupByKey、countByKey。

4、spark操作调优（spark算子调优，比较重要）：groupByKey，countByKey或aggregateByKey来重构实现。有些算子的性能，是比其他一些算子的性能要高的。foreachPartition替代foreach。如果一旦遇到合适的情况，效果还是不错的。

5、广播大变量

[spark性能调优都有哪些方法 - CSDN博客](https://blog.csdn.net/HANLIPENGHANLIPENG/article/details/78393450)
[spark性能优化 - 掘金](https://juejin.im/post/5a40b9bcf265da4312812653)

## 2. 对于Spark你觉得他对于现有大数据的现状的优势和劣势在哪里？

Spark的内存计算 主要体现在哪里？
(a) spark, 相比与map reduce最大的速度提升在于做重复计算时，spark可以重复使用相关的缓存数据，而M/R则会笨拙的不断进行disk i/o.
(b) 为了提高容错性，M/R所有的中间结果都会persist到disk， 而M/R 则默认保存在内存。
2.目前Spark主要存在哪些缺点？
(a) JVM的内存overhead太大，1G的数据通常需要消耗5G的内存 -> Project Tungsten 正试图解决这个问题；
(b) 不同的spark app之间缺乏有效的共享内存机制 -> Project Tachyon 在试图引入分布式的内存管理，这样不同的spark app可以共享缓存的数据

[spark与hadoop相比，存在哪些缺陷（劣势） - 云+社区 - 腾讯云](https://cloud.tencent.com/developer/article/1074623)

## 3. Spark的Shuffle原理及调优？

 Hash Based Shuffle
shuffle read shuffle writer 每个map 都需要为每个reducer 维护一个文件， 供 reducer 取来独缺，这就导致文件比较多 coslidate 减少了文件 通过 bucket 来维护
2. Sort Based Shuffle
1 引入，不会为每个reducer生成一个单独的文件，而是生成一个单独的文件，并且会生成一个Index文件，reducer 通过 shuffleblockmanager 去根据index 读取相应的文件

[用实例说明Spark stage划分原理 - bonelee - 博客园](https://www.cnblogs.com/bonelee/p/6039469.html)

![image](http://static.lovedata.net/jpg/2018/6/14/cf900b9cbd52d3a84fd8f04dba7c199f.jpg)

![image](http://static.lovedata.net/jpg/2018/7/13/f98a94e948fe9d9b63643db96606fab3.jpg)

## 4.spark如何保证宕机迅速恢复

## 5. RDD的原理以及持久化原理

![image](http://static.lovedata.net/jpg/2018/6/14/e224b35496ac4de184008bbb09894893.jpg)

## 6. spark排序实现流程，reduce端怎么实现的；

## 7.Spark的特点是什么

[Spark 特点 - CSDN博客](https://blog.csdn.net/qq_41455420/article/details/79451722)

 快速
Spark函数（类似于MapReduce）运行的时候，绝大多数的函数是可以在内存里面去迭代的。只有少部分的函数需要落地到磁盘
2. 易用性
 编写简单，支持80种以上的高级算子，支持多种语言，数据源丰富，可部署在多种集群中
3. 通用性
4. 兼容性
5. 容错性高。
 Spark引进了弹性分布式数据集RDD (Resilient Distributed Dataset) 的抽象，它是分布在一组节点中的只读对象集合，这些集合是弹性的，如果数据集一部分丢失，则可以根据“血统”（即充许基于数据衍生过程）对它们进行重建。另外在RDD计算时可以通过CheckPoint来实现容错，而CheckPoint有两种方式：CheckPoint Data，和Logging The Updates，用户可以控制采用哪种方式来实现容错。

## 8.Spark的三种提交模式是什么

- local(本地模式)：常用于本地开发测试，本地还分为local单线程和local-cluster多线程;
- standalone(集群模式)：典型的Mater/slave模式，不过也能看出Master是有单点故障的；Spark支持ZooKeeper来实现 HA
- on yarn(集群模式)： 运行在 yarn 资源管理器框架之上，由 yarn 负责资源管理，Spark 负责任务调度和计算
- on mesos(集群模式)： 运行在 mesos 资源管理器框架之上，由 mesos 负责资源管理，Spark 负责任务调度和计算
- on cloud(集群模式)：比如 AWS 的 EC2，使用这个模式能很方便的访问 Amazon的 S3;Spark 支持多种分布式存储系统：HDFS 和 S3

## 9.spark 实现高可用性：High Availability？

## 10. spark中怎么解决内存泄漏问题？

[Spark面对OOM问题的解决方法及优化总结 - CSDN博客](https://blog.csdn.net/yhb315279058/article/details/51035631)

## 1 Spark-submit模式yarn-cluster和yarn-client的区别

 yarn-client用于测试，因为他的Driver运行在本地客户端，会与yarn集群产生较大的网络通信，从而导致网卡流量激增；它的好处在于直接执行时，在本地可以查看到所有的log，方便调试；
2. yarn-cluster用于生产环境，因为Driver运行在NodeManager，相当于一个ApplicationMaster，没有网卡流量激增的问题；缺点在于调试不方便，本地用spark-submit提交后，看不到log，只能通过yarn application_id这种命令来查看，很麻烦

![image](http://static.lovedata.net/jpg/2018/7/16/b4e773e7305d67cf7f0f95475565e2b3.jpg)

![image](http://static.lovedata.net/jpg/2018/7/16/30a73b39fab370ec772c6a768bba0515.jpg)

![image](http://static.lovedata.net/jpg/2018/7/16/0fe9fe0e348a9ea7c258cdbe806610e7.jpg)

![image](http://static.lovedata.net/jpg/2018/7/16/87a6250639209f2a955781bfa8c5b8fd.jpg)

## 12.spark运行原理，从提交一个jar到最后返回结果，整个过程

 用户通过spark-submit脚本提交应用。
2. spark-submit根据用户代码及配置确定使用哪个资源管理器，以及在合适的位置启动driver。
3. driver与集群管理器(如YARN)通信，申请资源以启动executor。
4. 集群管理器启动executor。
5. driver进程执行用户的代码，根据程序中定义的transformation和action，进行stage的划分，然后以task的形式发送到executor。（通过DAGScheduler划分stage，通过TaskScheduler和TaskSchedulerBackend来真正申请资源运行task）
6. task在executor中进行计算并保存结果。
7. 如果driver中的main()方法执行完成退出，或者调用了SparkContext#stop()，driver会终止executor进程，并且通过集群管理器释放资源。

![image](http://static.lovedata.net/jpg/2018/6/25/08c8c49a405cc88b2ad3ec169b09ca18.jpg)

## Yarn-Cluster

步骤一：Client类提交应用到YARN ResourceManager，向RM申请资源作为AM
步骤二：在申请到的机器中启动driver，注册成为AM，并调用用户代码，然后创建SparkContext。（driver是一个逻辑概念，并不实际存在，通过抽象出driver这一层，所有的运行模式都可以说是在driver中调用用户代码了）
步骤三：SparkContext中创建DAGScheduler与YarnClusterScheduler与YarnClusterSchedulerBackend。当在用户代码中遇到action时，即会调用DAGScheduler的runJob，任务开始调度执行。
步骤四：YarnClusterSchedulerBackend在NodeManager上启动Executor
步骤五：Executor启动Task，开始执行任务

![image](http://static.lovedata.net/jpg/2018/6/25/db6d9c7a2c79d10b803760d02bccb7fjpg)

## Yarn-Client

![image](http://static.lovedata.net/jpg/2018/6/25/883bd9e6fef3896ed47d4c4fd942c19b.jpg)

参考
[spark提交应用的全流程分析 - CSDN博客](https://blog.csdn.net/jediael_lu/article/details/76735217)

## 13. spark的stage划分是怎么实现的？拓扑排序？怎么实现？还有什么算法实现？

宽依赖就是stage划分的依据

![image](http://static.lovedata.net/jpg/2018/7/13/5e1a7fa922dbf6c2d5e646071d1bcb47.jpg)

## 14. spark rpc，spark2.0为啥舍弃了akka，而用netty

## 15. spark的各种shuffle，与mapreduce的对比;

## 16. spark的各种ha，master的ha，worker的ha，executor的ha，driver的ha,task的ha,在容错的时候对集群或是task有什么影响？

## 17.spark的内存管理机制，spark6前后对比分析

## 18. spark2.0做出了哪些优化？tungsten引擎？cpu与内存两个方面分别说明

## 19.spark rdd、dataframe、dataset区别

## 20. HashPartitioner与RangePartitioner的实现，以及水塘抽样；

## 2 spark有哪几种join，使用场景，以及实现原理

## 22. dagschedule、taskschedule、schedulebankend实现原理；

![image](http://static.lovedata.net/jpg/2018/7/13/0d54689ccc31b5ded3369e8ae9656432.jpg)

![image](http://static.lovedata.net/jpg/2018/7/13/5ed17c17f013769c3defd665667653fe.jpg)

![image](http://static.lovedata.net/jpg/2018/7/13/c3df67b9b516c6bc25b7d4a70dfabcac.jpg)

![image](http://static.lovedata.net/jpg/2018/7/13/30383ea00b1199a91e48deb8b1b9563d.jpg)

stage 划分

![image](http://static.lovedata.net/jpg/2018/7/13/d48db9cdff54eb6cea4e1d9dcf0d7726.jpg)

> 宽依赖就是认为是Dagd的分界线,或者说根据宽依赖将job分为不同的阶段（stage）

![image](http://static.lovedata.net/jpg/2018/7/13/37054531f120f5531db40dcf90a03d13.jpg)

## 23. 宽依赖、窄依赖的概念？宽依赖、窄依赖的例子？以下图中所指的是何种依赖

![image](http://static.lovedata.net/jpg/2018/7/4/52cbbb0ea8777f912ef6f6383cc1f5eb.jpg)

![image](http://static.lovedata.net/jpg/2018/7/4/b2053b3741affbafd63dff5ca58ca0b7.jpg)
![image](http://static.lovedata.net/jpg/2018/6/22/cef170091c377f5f7840ca80a9f9287c.jpg)

![image](http://static.lovedata.net/jpg/2018/7/13/fddef8da2524d01a32515fd4a69837c9.jpg)

## 24. Spark数据倾斜，怎么定位、怎么解决（阿里）；

[spark提交应用的全流程分析 - CSDN博客](https://www.cnblogs.com/LHWorldBlog/p/850612html)

![【Spark篇】---Spark解决数据倾斜问题](http://static.lovedata.net/jpg/2018/6/14/a6f6512145359189c2a4e9f9afac7673.jpg)

## 25 spark有哪些组件？

- master：管理集群和节点，不参与计算。
- worker：计算节点，进程本身不参与计算，和master汇报。
- Driver：运行程序的main方法，创建spark context对象。
- spark context：控制整个application的生命周期，包括dagsheduler和task scheduler等组件。
- client：用户提交程序的入口。

## 26 Spark的适用场景

- 复杂的批量处理（Batch Data Processing），偏重点在于处理海量数据的能力，至于处理速度可忍受，通常的时间可能是在数十分钟到数小时；
- 基于历史数据的交互式查询（Interactive Query），通常的时间在数十秒到数十分钟之间
- 基于实时数据流的数据处理（Streaming Data Processing），通常在数百毫秒到数秒之间

## 27 spark 架构

![spark基础运行架构](http://static.lovedata.net/jpg/2018/6/14/a6f6512145359189c2a4e9f9afac7673.jpg)

![spark结合yarn集群背后的运行流程](http://static.lovedata.net/jpg/2018/6/14/34668f2bad595ff835fdf0823dfb99c6.jpg)

![image](http://static.lovedata.net/jpg/2018/6/14/3a907f0488496c7a39fb6bec02966e25.jpg)

## 28 spark task解析？

![image](http://static.lovedata.net/jpg/2018/7/13/f243b2f1b3fae98fda495c5fc8fb3fea.jpg)


## spark 集群 100g内存 有两百g文件，去读取，有什么问题。

 [内存有限的情况下 Spark 如何处理 T 级别的数据 - abcde - CSDN博客 ](https://blog.csdn.net/asdfsadfasdfsa/article/details/78606365)
2. 只有在用户要求Spark cache该RDD，且storage level要求在内存中cache时，Iterator计算出的结果才会被保留，通过cache manager放入内存池


## spark 内存划分



