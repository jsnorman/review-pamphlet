# 1. Flink 实现 exactly once 语义

[Flink  执行语义“Exactly once”详解 Asynchronous barrie... - 简书](https://www.jianshu.com/p/dd895ca12061)

Asynchronous barrier snapshots  ABS算法


![image](http://static.lovedata.net/jpg/2018/12/18/a9944194dba97d49147a99cbe3382e21.jpg)

[apache flink 保证端到端exactly-once语义的简介（同样适用于kafka！... - 简书](https://www.jianshu.com/p/9d875f6e54f2)


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



# 3. flink task slot 和 parallelism 介绍

[ApacheFlink高级特性与高级应用之slot和parallelism介绍 - 云计算技术频道 - 红黑联盟](https://www.2cto.com/net/201711/699128.html)


