# Impala

## 1. impala 特点

[Kudu+Impala介绍 | 微店数据科学团队博客](https://juejin.im/entry/5a72d3d1f265da3e4d730b37)

1. Impala作为老牌的SQL解析引擎，其面对即席查询(Ad-Hoc Query)类请求的稳定性和速度在工业界得到过广泛的验证
2. 没有存储，负责解析sql
3. 定位类似hive，关注即席查询sql快速解析
4. 长sql还是hive更合适
5. group by sql 使用内存计算，建议内存128G以上  **Hive使用MR，效率低，稳定性好**
6. 不支持高并发，由于单个sql执行代价较高
7. 不能代替hive
8. 至少需要128G以上内存，并且把80%分配给Impala
9. 不会对表数据Cache，仅仅缓存一些表数据等元数据
10. 可以使用 hive metastore **Hive 是必选组件**

## 2. Impala为什么会这么快？

为速度而生，执行效率优化，非MR模型，MR sql转换为MR原语，需要多层迭代，造成极大浪费

- impala尽可能把数据缓存在内存中，数据不落地就能完成sql查询
- 常驻进程，避免MR启动开销
- 专为SQL设计，减少迭代次数，避免不必要shuffle和sort
- 利用现代化高性能服务器
  - LLVM 生产动态代码
  - 协调控制磁盘io，控制吞吐，吞吐量最大化
  - 代码效率曾采用C++，提速
  - 程序内存使用上，利用C++天然优势，遵循极少内存使用原则

## 3. Impala 查询流程

[大数据时代快速SQL引擎-Impala](https://blog.csdn.net/yu616568/article/details/52431835)

![image](http://static.lovedata.net/jpg/2018/5/21/84f8934b8517992c953bdf693d06b162.jpg)

下图展示了执行select t1.n1, t2.n2, count(1) as c from t1 join t2 on t1.id = t2.id join t3 on t1.id = t3.id where t3.n3 between ‘a’ and ‘f’ group by t1.n1, t2.n2 order by c desc limit 100;查询的执行逻辑，首先Query Planner生成单机的物理执行计划，如下图所示：

![image](http://static.lovedata.net/jpg/2018/5/21/379355edbd81503c0f525b698f70e543.jpg)

![image](http://static.lovedata.net/jpg/2018/5/21/293fc15dcc24eafafc9e577f9850a1a0.jpg)

## 4. Impala的部署方式

## 4.1 混合部署

混合部署意味着将Impala集群部署在Hadoop集群之上，共享整个Hadoop集群的资源，
前者的优势是Impala可以和Hadoop集群共享数据，不需要进行数据的拷贝，但是存在Impala和Hadoop集群抢占资源的情况，进而可能影响Impala的查询性能

![image](http://static.lovedata.net/jpg/2018/5/21/8651108dd21a447b7f781417cfb4a353.jpg)

## 4.2 独立部署

独立部署则是单独使用部分机器只部署HDFS和Impala
而后者可以提供稳定的高性能，但是需要持续的从Hadoop集群拷贝数据到Impala集群上，增加了ETL的复杂度

![image](http://static.lovedata.net/jpg/2018/5/21/a022911b475d7424a30cc3b68673820a.jpg)

# 5 impala 做的一些优化


# 6 impala与hive的比较以及impala的优缺点

[impala与hive的比较以及impala的有缺点 - Gemini的博客 - CSDN博客](https://blog.csdn.net/gemini_two/article/details/78992484)

- 快速
- 基于内存  
- hive 基于 mr

优化技术 
- 没有使用 mr ， 但是mr 面向批处理，不是面向交互。
- impala 把查询分成执行计划树。不是mr。    拉式获取数据，减少中间结果罗破案步骤，在读取开销
- impla  基于服务，避免每次都要启动
- 使用 LLVM 产生代码。特定代码
- 充分利用可用的硬件指令
- 更好的IO调度，Impala知道数据块所在的磁盘位置能够更好的利用多磁盘的优势，同时Impala支持直接数据块读取和本地代码计算checksum
- 通过选择合适的数据存储格式可以得到最好的性能（Impala支持多种存储格式）。
- 最大使用内存，中间结果不写磁盘，及时通过网络以stream的方式传递。

 Impala与Hive的异同

相同点：

数据存储：使用相同的存储数据池都支持把数据存储于HDFS, HBase。
元数据：两者使用相同的元数据。
SQL解释处理：比较相似都是通过词法分析生成执行计划。

不同点
  执行计划  hive 依赖mr， 执行计划划分，  mr  shuffle,  转换多轮 mr ，增加执行时间
   impala  表现为一个完整执行计划树  分发计划，impalad 不用像hive 组合成管道性  mr   更好并发性，避免不必要 中间 sort  shuffle
   
   
   
 数据流 
 hive ： 推。每个机节点计算完成后主动推给后续节点
 impala ： 拉
 
 ![image](http://static.lovedata.net/jpg/2018/12/18/a47ebe72052fd388e75b19e1ae4d23b5.jpg)
 
 ![image](http://static.lovedata.net/jpg/2018/12/18/a494fb8d3e1aafde87dad14d27e02884.jpg)
 

## 7。 当数据集超出可用内存时会发生什么


## 8  哪些是内存密集型操作？

假如查询失败，错误信息是 “memory limit exceeded”，你可能怀疑有内存泄露(memory leak)。其实问题可能是因为查询构造的方式导致 Impala 分配超出你预期的内存，从而在某些节点上超出 Impala 分配的内存限制(The problem could actually be a query that is structured in a way that causes Impala to allocate more memory than you expect, exceeded the memory allocated for Impala on a particular node)。一些特别内存密集型的查询和表结构如下：

**使用动态分区的 INSERT 语句**，插入到包含许多分区的表中(特别是使用 Parquet 格式的表，这些表中每一个分区的数据都保存到内存中，直到它达到 1 GB 并被写入到硬盘里)。考虑把这样的操作分散成几个不同的 INSERT 语句，例如一次只加载一年的数据而不是一次加载所有年份的数据 

**在唯一或高基数(high-cardinality)列上的 GROUP BY 操作**。Impala 为 GROUP BY 查询中每一个不同的值分配一些处理结构(handler structures)。成千上万不同的 GROUP BY 值可能超出内存限制 
查询涉及到非常宽、包含上千个列的表，特别是包含许多 STRING 列的表。因为 Impala 允许 STRING 值最大不超过 32 KB，这些查询的中间结果集可能需要大量的内存分配


## 9.  Impala使用场景


1. 什么情况下适合使用 Impala 而不适合 Hive 和 MapReduce？

Impala 非常适合在大的数据集上，为交互式探索分析执行 SQL。Hive 和 MapReduce 则适合长时间运行的、批处理的任务，例如 ETL。
2. Impala 是否需要 MapReduce ？如果 MapReduce 停了，Impala 是否能正常工作？

Impala 用不到 MapReduce。
3. Impala 是否可以用于复杂事件处理？

例如，在工业环境中，许多客户端可能产生大量的数据。Impala 是否可用与分析这些数据，发现环境中显著的变化？

复杂事件处理(Complex Event Processing,CEP) 通常使用专门的流处理系统处理。Impala 不是流处理系统，它其实更像关系数据库。











