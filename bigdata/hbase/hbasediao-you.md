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
