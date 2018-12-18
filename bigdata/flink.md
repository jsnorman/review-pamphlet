# 1. Flink 实现 exactly once 语义

[Flink  执行语义“Exactly once”详解 Asynchronous barrie... - 简书](https://www.jianshu.com/p/dd895ca12061)

Asynchronous barrier snapshots  ABS算法


![image](http://static.lovedata.net/jpg/2018/12/18/a9944194dba97d49147a99cbe3382e21.jpg)

[apache flink 保证端到端exactly-once语义的简介（同样适用于kafka！... - 简书](https://www.jianshu.com/p/9d875f6e54f2)


## 检查点机制

在出现故障时候重新重置回正确的状态。、

三个人一起数珠子的游戏， 三个人一起数珠子， 又想保持一致，怎么办，如何记住， 如果一个人被打断了，可能就要重数，甚至整个重数哟。  


# 2.flink 运行时架构

![image](http://static.lovedata.net/jpg/2018/12/18/31a5a1466cf4e2031eeb420f8352fd5a.jpg)