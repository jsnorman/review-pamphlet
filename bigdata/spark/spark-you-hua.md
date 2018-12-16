
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





