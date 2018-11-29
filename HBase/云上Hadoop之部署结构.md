# 云上Hadoop之部署结构

在云上部署Hadoop是比较灵活的，可以根据不同的业务目标部署Hadoop集群，笔者总结了在云上的大体部署结构。

经典模式

传统的部署模式如上，线下的机器比较固定，所以一般的节点上会部署Datanode、NodeManager节点。

经典模式2

一般把节点分为：Master Node、Core Node、Task Node。这么部署结构相对灵活，也就是Task Node就部署NodeManager。如果需要添加计算能力，就添加Task Node。且因为Task Node无状态，在缩小集群时也相对简单。（这样就避免了在线下经常出现的，计算与存储不匹配的问题，导致资源浪费）
存储计算分离

在此模式下，数据放在OSS中，可以启动Hadoop集群分析数据。这种模式最大的好处就是Hadoop集群可以用完就释放掉，最大节约成本，E-Mapreduce也会提供按需计费的模式。
在一些常见的业务模式下，用户需要的离线分析只需要在晚上一段时间内完成即可。这样，在半夜启动一个集群，分析OSS已经存放的数据非常合适。
tips:HADOOP-12756 Hadoop会支持读取阿里云的OSS
第一个图的做法是，提供一个OSSFileSystem。第二个图的做法是，底层的HDFS直接代理走到OSS中，把HDFS做成无状态的，这么做的好处就是HDFS可以做一些类似alluxio的功能 ，且对上层完全透明。(目前还没有实现)

详情请阅读原文：https://yq.aliyun.com/articles/664948
