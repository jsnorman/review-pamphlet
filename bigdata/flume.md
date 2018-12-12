# Flume

## 1. flume flie channel 和 memchannel 的区别
[实时事件统计项目：优化flume:用file channel代替mem channel - arli_xu的博客 - CSDN博客](https://blog.csdn.net/arli_xu/article/details/83034511)

memchannel 可能占用内存过大，如果一段时间内数据暴增，可能导致内存被占满，然后就导致消息不及处理，丢数据，使用file就不会有这个问题。因为磁盘的容量要远远大于内存容量的。
