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

### G1 GC 分 Region 单独GC
    尽力延迟Full GC到来时间。MaxGCPauseMillis  参数来控制一旦发生Full GC的时候
的最大暂停时间， 避免时间太长造成RegionServer自

![image](http://static.lovedata.net/jpg/2018/12/17/f679ec23fa4361f37ebaee0ee0ee1602.jpg)