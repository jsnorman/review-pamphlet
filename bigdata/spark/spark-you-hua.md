
# Spark 优化

## 1 reduceByKey或者aggregateByKey与groupByKey的区别？

因为reduceByKey和aggregateByKey算子都会使用用户自定义的函数对每个节点本地的相同key进行预聚合。而groupByKey算子是不会进行预聚合的，全量的数据会在集群的各个节点之间分发和传输，性能相对来说比较差。

比如如下两幅图，就是典型的例子，分别基于reduceByKey和groupByKey进行单词计数。其中第一张图是groupByKey的原理图，可以看到，没有进行任何本地聚合时，所有数据都会在集群节点之间传输；第二张图是reduceByKey的原理图，可以看到，每个节点本地的相同key数据，都进行了预聚合，然后才传输到其他节点上进行全局聚合。

![image](http://static.lovedata.net/jpg/2018/6/14/31d0199949271ef1641a7be918818fcd.jpg)

## 2 如何使用高性能的算子？

1. 使用reduceByKey/aggregateByKey替代groupByKey
 使用mapPartitions替代普通map 可能出现OOM 因为可能一个分区数据量太大
3. 使用foreachPartitions替代foreach 比如讲分区数据写入到mysql
4. 使用filter之后进行coalesce操作 减少分区的数量
5. 使用repartitionAndSortWithinPartitions替代repartition与sort类操作
1. repartitionAndSortWithinPartitions是Spark官网推荐的一个算子，官方建议，如果需要在repartition重分区之后，还要进行排序，建议直接使用repartitionAndSortWithinPartitions算子。因为该算子可以一边进行重分区的shuffle操作，一边进行排序。shuffle与sort两个操作同时进行，比先shuffle再sort来说，性能可能是要高的。

参考
[Spark性能优化指南——基础篇 -](https://tech.meituan.com/spark-tuning-basic.html)

## 3 Spark Shuffle 数据倾斜的解决方案

1. 解决方案一：使用Hive ETL预处理数据 适用Hive join
 解决方案二：过滤少数导致倾斜的key
3. 解决方案三：提高shuffle操作的并行度 spark.sql.shuffle.partitions reduceByKey(1000)
4. 解决方案四：两阶段聚合（局部聚合+全局聚合）打随机数，预聚合，然后去掉随机数，在进行全局聚合 适用于聚合类型，如果是join类的shuffle操作，还得用其他的解决方案。
1. ![image](http://static.lovedata.net/jpg/2018/6/14/6d4482e3b2738463ed2ce881be07244b.jpg)
5. 解决方案五：将reduce join转为map join 在对RDD使用join类操作，或者是在Spark SQL中使用join语句时，而且join操作中的一个RDD或表的数据量比较小（比如几百M或者一两G），比较适用此方案。
1. 不使用join算子进行连接操作，而使用Broadcast变量与map类算子实现join操作，进而完全规避掉shuffle类的操作，彻底避免数据倾斜的发生和出现。
 对join操作导致的数据倾斜，效果非常好，因为根本就不会发生shuffle，也就根本不会发生数据倾斜。 适用场景较少，因为这个方案只适用于一个大表和一个小表的情况
6. 解决方案六：采样倾斜key并分拆join操作
1. 采样倾斜key并分拆join操作
方案适用场景：两个RDD/Hive表进行join的时候，如果数据量都比较大，无法采用“解决方案五”，那么此时可以看一下两个RDD/Hive表中的key分布情况。如果出现数据倾斜，是因为其中某一个RDD/Hive表中的少数几个key的数据量过大，而另一个RDD/Hive表中的所有key都分布比较均匀，那么采用这个解决方案是比较合适的。
方案实现思路：
对包含少数几个数据量过大的key的那个RDD，通过sample算子采样出一份样本来，然后统计一下每个key的数量，计算出来数据量最大的是哪几个key。
然后将这几个key对应的数据从原来的RDD中拆分出来，形成一个单独的RDD，并给每个key都打上n以内的随机数作为前缀，而不会导致倾斜的大部分key形成另外一个RDD。
接着将需要join的另一个RDD，也过滤出来那几个倾斜key对应的数据并形成一个单独的RDD，将每条数据膨胀成n条数据，这n条数据都按顺序附加一个0~n的前缀，不会导致倾斜的大部分key也形成另外一个RDD。
再将附加了随机前缀的独立RDD与另一个膨胀n倍的独立RDD进行join，此时就可以将原先相同的key打散成n份，分散到多个task中去进行join了。
而另外两个普通的RDD就照常join即可。
最后将两次join的结果使用union算子合并起来即可，就是最终的join结果。
![image](http://static.lovedata.net/jpg/2018/6/14/de04f1729bec2ba1c5b6569952473d38.jpg)
7. 解决方案七：使用随机前缀和扩容RDD进行join

[Spark性能优化指南——高级篇 -](https://tech.meituan.com/spark-tuning-pro.html)


## 4. spark streaming 的性能调优

![image](http://static.lovedata.net/jpg/2018/12/16/b6f7185e3b25402acca66b6e97943ef6.jpg)

![image](http://static.lovedata.net/jpg/2018/12/16/b1fd6a911ecdaf5ac048831b742b5bc6.jpg)

![image](http://static.lovedata.net/jpg/2018/12/16/696fd57c6272b46d3b48207d7e3d4982.jpg)

![image](http://static.lovedata.net/jpg/2018/12/16/77c9ea861c5fce256806dafda01164d5.jpg)

![image](http://static.lovedata.net/jpg/2018/12/16/6497d5e65fbbdc6d2fc59a9d29d468f8.jpg)

![image](http://static.lovedata.net/jpg/2018/12/16/063ed61132a76114a23616b18592ed49.jpg)

![image](http://static.lovedata.net/jpg/2018/12/16/419e4094cb076511e466ad5c6875c633.jpg)

![image](http://static.lovedata.net/jpg/2018/12/16/24ec91cacd8e116907ba9bd49437ba2a.jpg)


![image](http://static.lovedata.net/jpg/2018/12/16/598989ae837b34b5264aad91df491969.jpg)

![image](http://static.lovedata.net/jpg/2018/12/16/4878f666c57d9b505e5ef7c450133159.jpg)

## 5 spark 常见问题和解决

[Spark程序运行常见错误解决方法以及优化 - sdujava2011 - CSDN博客](https://blog.csdn.net/sdujava2011/article/details/49796439)


## 6 spark 优化

[Spark性能优化指南——基础篇 - R星月 - 博客园](https://www.cnblogs.com/rxingyue/p/7113079.html)


### 开发调优
原则一：避免创建重复的RDD

原则二：尽可能复用同一个RDD

原则三：对多次使用的RDD进行持久化


原则四：尽量避免使用shuffle类算子 使用广播变量

原则五：使用map-side预聚合的shuffle操作

原则六：使用高性能的算子

原则七：广播大变量

原则八：使用Kryo优化序列化性能  默认为 jdk 序列化

原则九：优化数据结构

因此Spark官方建议，在Spark编码实现中，特别是对于算子函数中的代码，尽量不要使用上述三种数据结构，尽量使用字符串替代对象，使用原始类型（比如Int、Long）替代字符串，使用数组替代集合类型，这样尽可能地减少内存占用，从而降低GC频率，提升性能。


### 资源调优
![image](http://static.lovedata.net/jpg/2018/12/18/2ac2a9c74a0a64fd9cf451786d5c90cd.jpg)
资源参数调优
了解完了Spark作业运行的基本原理之后，对资源相关的参数就容易理解了。所谓的Spark资源参数调优，其实主要就是对Spark运行过程中各个使用资源的地方，通过调节各种参数，来优化资源使用的效率，从而提升Spark作业的执行性能。以下参数就是Spark中主要的资源参数，每个参数都对应着作业运行原理中的某个部分，我们同时也给出了一个调优的参考值。
num-executors

    参数说明：该参数用于设置Spark作业总共要用多少个Executor进程来执行。Driver在向YARN集群管理器申请资源时，YARN集群管理器会尽可能按照你的设置来在集群的各个工作节点上，启动相应数量的Executor进程。这个参数非常之重要，如果不设置的话，默认只会给你启动少量的Executor进程，此时你的Spark作业的运行速度是非常慢的。
    参数调优建议：每个Spark作业的运行一般设置50~100个左右的Executor进程比较合适，设置太少或太多的Executor进程都不好。设置的太少，无法充分利用集群资源；设置的太多的话，大部分队列可能无法给予充分的资源。

executor-memory

    参数说明：该参数用于设置每个Executor进程的内存。Executor内存的大小，很多时候直接决定了Spark作业的性能，而且跟常见的JVM OOM异常，也有直接的关联。
    参数调优建议：每个Executor进程的内存设置4G~8G较为合适。但是这只是一个参考值，具体的设置还是得根据不同部门的资源队列来定。可以看看自己团队的资源队列的最大内存限制是多少，num-executors乘以executor-memory，是不能超过队列的最大内存量的。此外，如果你是跟团队里其他人共享这个资源队列，那么申请的内存量最好不要超过资源队列最大总内存的1/3~1/2，避免你自己的Spark作业占用了队列所有的资源，导致别的同学的作业无法运行。

executor-cores

    参数说明：该参数用于设置每个Executor进程的CPU core数量。这个参数决定了每个Executor进程并行执行task线程的能力。因为每个CPU core同一时间只能执行一个task线程，因此每个Executor进程的CPU core数量越多，越能够快速地执行完分配给自己的所有task线程。
    参数调优建议：Executor的CPU core数量设置为2~4个较为合适。同样得根据不同部门的资源队列来定，可以看看自己的资源队列的最大CPU core限制是多少，再依据设置的Executor数量，来决定每个Executor进程可以分配到几个CPU core。同样建议，如果是跟他人共享这个队列，那么num-executors * executor-cores不要超过队列总CPU core的1/3~1/2左右比较合适，也是避免影响其他同学的作业运行。

driver-memory

    参数说明：该参数用于设置Driver进程的内存。
    参数调优建议：Driver的内存通常来说不设置，或者设置1G左右应该就够了。唯一需要注意的一点是，如果需要使用collect算子将RDD的数据全部拉取到Driver上进行处理，那么必须确保Driver的内存足够大，否则会出现OOM内存溢出的问题。

spark.default.parallelism

    参数说明：该参数用于设置每个stage的默认task数量。这个参数极为重要，如果不设置可能会直接影响你的Spark作业性能。
    参数调优建议：Spark作业的默认task数量为500~1000个较为合适。很多同学常犯的一个错误就是不去设置这个参数，那么此时就会导致Spark自己根据底层HDFS的block数量来设置task的数量，默认是一个HDFS block对应一个task。通常来说，Spark默认设置的数量是偏少的（比如就几十个task），如果task数量偏少的话，就会导致你前面设置好的Executor的参数都前功尽弃。试想一下，无论你的Executor进程有多少个，内存和CPU有多大，但是task只有1个或者10个，那么90%的Executor进程可能根本就没有task执行，也就是白白浪费了资源！因此Spark官网建议的设置原则是，设置该参数为num-executors * executor-cores的2~3倍较为合适，比如Executor的总CPU core数量为300个，那么设置1000个task是可以的，此时可以充分地利用Spark集群的资源。

spark.storage.memoryFraction

    参数说明：该参数用于设置RDD持久化数据在Executor内存中能占的比例，默认是0.6。也就是说，默认Executor 60%的内存，可以用来保存持久化的RDD数据。根据你选择的不同的持久化策略，如果内存不够时，可能数据就不会持久化，或者数据会写入磁盘。
    参数调优建议：如果Spark作业中，有较多的RDD持久化操作，该参数的值可以适当提高一些，保证持久化的数据能够容纳在内存中。避免内存不够缓存所有的数据，导致数据只能写入磁盘中，降低了性能。但是如果Spark作业中的shuffle类操作比较多，而持久化操作比较少，那么这个参数的值适当降低一些比较合适。此外，如果发现作业由于频繁的gc导致运行缓慢（通过spark web ui可以观察到作业的gc耗时），意味着task执行用户代码的内存不够用，那么同样建议调低这个参数的值。

spark.shuffle.memoryFraction

    参数说明：该参数用于设置shuffle过程中一个task拉取到上个stage的task的输出后，进行聚合操作时能够使用的Executor内存的比例，默认是0.2。也就是说，Executor默认只有20%的内存用来进行该操作。shuffle操作在进行聚合时，如果发现使用的内存超出了这个20%的限制，那么多余的数据就会溢写到磁盘文件中去，此时就会极大地降低性能。
    参数调优建议：如果Spark作业中的RDD持久化操作较少，shuffle操作较多时，建议降低持久化操作的内存占比，提高shuffle操作的内存占比比例，避免shuffle过程中数据过多时内存不够用，必须溢写到磁盘上，降低了性能。此外，如果发现作业由于频繁的gc导致运行缓慢，意味着task执行用户代码的内存不够用，那么同样建议调低这个参数的值。




