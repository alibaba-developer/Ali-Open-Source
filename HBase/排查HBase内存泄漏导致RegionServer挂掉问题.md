# 排查HBase内存泄漏导致RegionServer挂掉问题

问题描述
在测试Phoenix稳定性时，发现HBase集群其中一台RegionServer节点FullGC严重，隔一段时间就会挂掉。

HBase集群规格

初步分析
使用jstat监控RegionServer的Heap Size和垃圾回收情况

Old区内存一直在90%多，且FullGC次数一直在增多。

通过ganglia查看集群的FullGC情况，也可以看出003节点持续在FullGC，并最终挂掉。

怀疑存在内存泄漏导致FullGC可回收的内存越来越小，回收的时间也越来越长，最终导致RegionServer心跳超时，被Master干掉。

问题定位
排查内存泄漏问题，可借助jmap分析java堆内存的占用情况，jmap使用参考：https://blog.csdn.net/xidiancoder/article/details/70948569

使用jmap将RegionServer堆内存dump下来：

详情请阅读原文：https://yq.aliyun.com/articles/669056
