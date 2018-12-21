****# 1. Flink 实现 exactly once 语义

[Flink  执行语义“Exactly once”详解 Asynchronous barrie... - 简书](https://www.jianshu.com/p/dd895ca12061)

Asynchronous barrier snapshots  ABS算法


![image](http://static.lovedata.net/jpg/2018/12/18/a9944194dba97d49147a99cbe3382e21.jpg)

[apache flink 保证端到端exactly-once语义的简介（同样适用于kafka！... - 简书](https://www.jianshu.com/p/9d875f6e54f2)


![image](http://static.lovedata.net/jpg/2018/12/19/314c8e396615bdc6539eb235317fe7d5.jpg)

![image](http://static.lovedata.net/jpg/2018/12/19/a1aee54c0d3e156d4f90b152d2c0383e.jpg)


2.FlinkKafkaConsumer08通过Kafka的低级API和Flink带barrier的轻量级checkpoint机制保证了在高吞吐量的情况下的exactly-once。

[开发者干货 | 当Flink遇到Kafka -     FlinkKafkaConsumer使用详解](https://www.sohu.com/a/168546400_617676)

当谈及仅一次处理时，**我们真正想表达的是每条输入消息只会影响最终结果一次**   ！【译者：影响应用状态一次，而非被处理一次】即使出现机器故障或软件崩溃，Flink也要保证不会有数据被重复处理或压根就没有被处理从而影响状态

Flink开发出了**checkpointing**机制，而它则是提供这种应用内仅一次处理的基石。

简单来说，一个Flink checkpoint是一个一致性快照
1. 应用的当前状态

2. 消费的输入流位置


在Flink 1.4版本之前，仅一次处理只限于Flink应用内。  后面的版本支持两阶段提交。   **TwoPhaseCommitSinkFunction**

**Kafka 0.11.0.0版本正式发布了对于事务的支持**——这是与Kafka交互的Flink应用要实现端到端仅一次语义的必要条件。

![image](http://static.lovedata.net/jpg/2018/12/19/eabe03234af3da5dabe26f0b1937d546.jpg)

在一个分布式且含有多个并发执行sink的应用中，仅仅执行单次提交或回滚是不够的，因为所有组件都必须对这些提交或回滚达成共识，这样才能保证得到一个一致性的结果。Flink使用两阶段提交协议以及预提交(pre-commit)阶段来解决这个问题。


![image](http://static.lovedata.net/jpg/2018/12/19/8f3d0f1471871b25afbd91fc4b363ecd.jpg)


[干货:Flink+Kafka 0.11端到端精确一次处理语义实现 - Spark高级玩法 - CSDN博客](https://blog.csdn.net/rlnLo2pNEfx9c/article/details/81369878)

[使用 Flink 进行高吞吐，低延迟和 Exactly-Once 语义流处理 | Coder·码农网](https://www.codercto.com/a/26047.html)



## 检查点机制

在出现故障时候重新重置回正确的状态。、

三个人一起数珠子的游戏， 三个人一起数珠子， 又想保持一致，怎么办，如何记住， 如果一个人被打断了，可能就要重数，甚至整个重数哟。  一个好的方法，就是在珠子上，每隔一段就系上一个橡皮筋，可以和珠子一起懂，安排一个助手，让他在你和朋友拨到皮筋的时候记录总数，用这种方法，有人输错的时候，就不必从头开始数。 可以从上一次皮筋出开始重数。  助手会告诉你每个人重数时候的起始数值， 例如皮筋出的数值是多少？？


检查点，就类似于皮筋标记。。。    总状态 在每颗珠子波动之后更新一次， 助手则会保存与没跟皮筋对应的检查点状态，，这种方法让重新 计算变得简单。


# 2.flink 运行时架构

![image](http://static.lovedata.net/jpg/2018/12/18/31a5a1466cf4e2031eeb420f8352fd5a.jpg)

[Flink原理与实现：架构和拓扑概览 - huangshulang1234的博客 - CSDN博客](https://blog.csdn.net/huangshulang1234/article/details/78666904)

![image](http://static.lovedata.net/jpg/2018/12/19/bd964c6c7b75fbf8fa29dc4983b9a30a.jpg)


![image](http://static.lovedata.net/jpg/2018/12/19/3bfe18f298c9dfb9c65eb31160bf6cf6.jpg)

![image](http://static.lovedata.net/jpg/2018/12/19/154f305bd931c83bc502d1dc2a751741.jpg)

StreamGraph client 断生成，

 ![image](http://static.lovedata.net/jpg/2018/12/19/14b10d6b8ab3e4d1c6b6ed626b566ffe.jpg)

![image](http://static.lovedata.net/jpg/2018/12/19/97cbd95ddb28550a45bb2a62c34efd5f.jpg)


![image](http://static.lovedata.net/jpg/2018/12/19/18ea0ef010c3ffd585d8d2b49339fa09.jpg)
ExecuteiGraph 是 JobGraph 的 并行化，打散哟。

![image](http://static.lovedata.net/jpg/2018/12/19/22dd982bacb5fe7bee8500011e557d92.jpg)

![image](http://static.lovedata.net/jpg/2018/12/19/86e18fdf4994bf07f68bb5ae17ad1659.jpg)

![image](http://static.lovedata.net/jpg/2018/12/19/e1fe6aff1099ad90891d52671d8c90d1.jpg)

# 3. flink task slot 和 parallelism 介绍

[ApacheFlink高级特性与高级应用之slot和parallelism介绍 - 云计算技术频道 - 红黑联盟](https://www.2cto.com/net/201711/699128.html)


# 4. flink  window 

![image](http://static.lovedata.net/jpg/2018/12/19/dcf5266ee5f1678a27a80eff417d5d15.jpg)

![image](http://static.lovedata.net/jpg/2018/12/19/cd4080e4f7c9e59b360c48ba1136b74d.jpg)

![image](http://static.lovedata.net/jpg/2018/12/19/ea6cd9344ce4fb46c9bce617370b54c1.jpg)

![image](http://static.lovedata.net/jpg/2018/12/19/ab65f1fbbb8e77c66de87447e69f5998.jpg)

![image](http://static.lovedata.net/jpg/2018/12/19/575621c9d99d1f3190b0b74f0999de36.jpg) 

![image](http://static.lovedata.net/jpg/2018/12/19/7ea77f6cddf30dfdbf623a782cc1978a.jpg)


# 5 flink time 类型
![image](http://static.lovedata.net/jpg/2018/12/19/14d0f2ec99e2cce52bf6e0fb46239937.jpg)

![image](http://static.lovedata.net/jpg/2018/12/19/3a15271c14cebedba3f954d90e02751a.jpg)

![image](http://static.lovedata.net/jpg/2018/12/19/9ce2647f74831ab153afed6cd97d0345.jpg)


![image](http://static.lovedata.net/jpg/2018/12/19/673cdde282c6e933a3194565b1facfe5.jpg)



![image](http://static.lovedata.net/jpg/2018/12/19/a04a7670f524d8a91b15c843757e9097.jpg)

![image](http://static.lovedata.net/jpg/2018/12/19/48b66e235bbeec64b39fd90f71c0eba3.jpg)

allowLatency 窗口销毁时间延时， 比如延时 十秒钟，但是会重复计算窗口，造成数据异常

![image](http://static.lovedata.net/jpg/2018/12/19/5867ddda990fb30931577845257e6069.jpg)


# 6 flink  source  sink

![image](http://static.lovedata.net/jpg/2018/12/19/60e8da9a0c69904f783249af9b33eeee.jpg)


# 7. flink spark  storm 的对比


Steaming










