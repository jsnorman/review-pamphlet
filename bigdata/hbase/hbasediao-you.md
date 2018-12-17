# 1. Master和RegionServer的JVM调优

## 先调大堆内存

![image](http://static.lovedata.net/jpg/2018/12/17/69ba78e80c45e2bfb925f41d4dc96ff9.jpg)

PermSize的调整


![image](http://static.lovedata.net/jpg/2018/12/17/1410cecb7e88b7615afdd0645e277702.jpg)

![image](http://static.lovedata.net/jpg/2018/12/17/6e674cc53ed9ec18fb9c440f79166722.jpg)

![image](http://static.lovedata.net/jpg/2018/12/17/54ffc1996c67a55004efe8c118389a3d.jpg)


![image](http://static.lovedata.net/jpg/2018/12/17/1410cecb7e88b7615afdd0645e277702.jpg)

## 垃圾回收调优

### CMS


![image](http://static.lovedata.net/jpg/2018/12/17/4a435a6eaaed8b8200820cbf4b6867b3.jpg)


![image](http://static.lovedata.net/jpg/2018/12/17/4bb23b42fdca1dd6201882ca23e6b2e9.jpg)

### G1 GC 分 Region 单独GC  大内存下适用
尽力延迟Full GC到来时间。MaxGCPauseMillis  参数来控制一旦发生Full GC的时候
的最大暂停时间， 避免时间太长造成RegionServer自   这里说的堆内存很大的情况， 具体指的是多大
大概是32GB、 64GB或者以上， 这样才算大内存

![image](http://static.lovedata.net/jpg/2018/12/17/f679ec23fa4361f37ebaee0ee0ee1602.jpg)

### 选择

![image](http://static.lovedata.net/jpg/2018/12/17/84a3fe8a0412dad42adcd7691b52a19b.jpg)


##  Memstore的专属JVM策略MSLAB

CMS 合并碎片也可能很慢，导致zk认为已经死掉了

![image](http://static.lovedata.net/jpg/2018/12/17/dcd119b31f3de65a4a5c258f6013fdc8.jpg)

![image](http://static.lovedata.net/jpg/2018/12/17/37ee2f474dc9f13fc3f6f6db13530d8a.jpg)


其实JVM为了避免这个问题有一个基于线程的解决方案， 叫
TLAB（ Thread-Local allocation buffer） 。 当你使用TLAB的时候， 每
一个线程都会分配一个固定大小的内存空间， 专门给这个线程使用， 当
线程用完这个空间后再新申请的空间还是这么大， 这样下来就不会出现特别小的碎片空间， 基本所有的对象都可以有地方放。 缺点就是无论你
的线程里面有没有对象都需要占用这么大的内存， 其中有很大一部分空
间是闲置的， 内存空间利用率会降低。 不过能避免Full GC， 这些都是
值得的。
但是HBase不能直接使用这个方案， 因为在HBase中多个Region是被
一个线程管理的， 多个Memstore占用的空间还是无法合理地分开。 于是
HBase就自己实现了一套以Memstore为最小单元的内存管理机制， 称为
MSLAB（ Memstore-Local Allocation Buffers） 。 这套机制完全沿袭了
TLAB的实现思路， 只不过内存空间是由Memstore来分配的

![image](http://static.lovedata.net/jpg/2018/12/17/6f9b9e6a271a69be879e8ef41533262c.jpg)

这样就消除了小
碎片引起的无法插入数据问题， 但是会降低内存**利用率**， 因为就算你的
chunk里面只放1KB的数据， 这个chunk也要占2MB的大小。 不过， 为了不
发生Full GC， 这些都可以忍

你可能会觉得G1GC跟MSLAB的实现思
路非常接近， 那为什么还要发明MSLAB策略呢？ 因为G1GC是MSLAB发明后
才出现的策略。

## WAL的优化
![image](http://static.lovedata.net/jpg/2018/12/17/a34156a5947a1d4de335bd0ea3a8973c.jpg)

##  BlockCache的优化

![image](http://static.lovedata.net/jpg/2018/12/17/89d8c6349e48b9d0a3bfdd7403ca7a43.jpg)

BlockCache的工作原理就跟你们猜想的一样了： 读请求到HBase之
后先尝试查询BlockCache， 如果获取不到就去HFile（ StoreFile） 和
Memstore中去获取。 如果获取到了则在返回数据的同时把Block块缓存
到BlockCache中。


### LRUBlock Cache
之前只有这种BlockCache的实现方案。 LRU就是Least Recently Used，即近期最少使用算法的缩写。 读出来的block会被放到BlockCache中待
下次查询使用。 当缓存满了的时候， 会根据LRU的算法来淘汰block。
LRUBlockCache被分为三个区域， 如表8-5所示

![image](http://static.lovedata.net/jpg/2018/12/17/ee5b4cef804b0b0971de116f3670bfaf.jpg)

的
列族被设置为IN-MEMORY并不是意味着这个列族是存储在内存中
的。 这个列族依然是 跟别的列族一样存储在硬盘上。 一般的Block被第
一次读出后是放到single-access的， 只有当被访问多次后才会放到
multi-access， 而带有IN-MEMORY属性的列族中的Block一开始就被放到
in-memory区域。 这个区域的缓存有最高的存活时间， 在需要淘汰Block
的时候， 这个区域的Block是最后被考虑到的， 所以这个属性仅仅是为
了BlockCache而创造的。

- 缺点

完全基于JVM Heap的缓存， 势必带来一个后果： 随着内存中对象越
来越多， 每隔一段时间都会引发一次Full GC。 凡是做了几年Java的人
听到Full GC都会浑身一颤。 在Full GC的过程中， 整个JVM完全处于停
滞状态， 有的时候长达几分钟

##  Bucket Cache  堆外内存

![image](http://static.lovedata.net/jpg/2018/12/17/56834eba10cd96bcfe64ce29c9fefb61.jpg)


用法：

- ![image](http://static.lovedata.net/jpg/2018/12/17/5e21fb96dc3dc94a77a989a2cd934c72.jpg)
- ![image](http://static.lovedata.net/jpg/2018/12/17/34c5d3e1fa2a06798caeb9ab553943b8.jpg)
![image](http://static.lovedata.net/jpg/2018/12/17/739805ebd06c047f8a92c896a5f40643.jpg)

在BucketCache的时代， 也不是单纯地使用BucketCache， 但是这回
不是一二级缓存的结合； 而是另一种模式， 叫组合模式
（ CombinedBlockCahce） 。 具体地说就是把不同类型的Block分别放到
LRUCache和BucketCache中。

Index Block和Bloom Block会被放到LRUCache中。 Data Block被直
接放到BucketCache中， 所以数据会去LRUCache查询一下， 然后再去
BucketCache中查询真正的数据。 其实这种实现是一种更合理的二级缓
存， 数据从一级缓存到二级缓存最后到硬盘， 数据是从小到大， 存储介
质也是由快到慢。 考虑到成本和性能的组合， 比较合理的介质是：
LRUCache使用内存->BuckectCache使用SSD->HFile使用机械硬盘

