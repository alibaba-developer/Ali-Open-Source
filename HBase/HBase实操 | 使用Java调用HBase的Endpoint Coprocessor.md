# HBase实操 | 使用Java调用HBase的Endpoint Coprocessor

1.文档编写目的

HBase是一款基于Hadoop的Key-Value数据库，提供了对HDFS上数据的高效随机读写服务，填补了Hadoop MapReduce批处理的缺陷，但HBase作为列簇数据库无法轻易的建立“二级索引”、难以执行求和、计数、排序等操作。在HBase0.96版本后引入了协处理器(Coprocessor)，用户可以编写运行在HBase Server端的代码。HBase支持两种类型的协处理器，Endpoint和Observer。

Endpoint协处理器类似传统数据库中的存储过程，客户端可以调用这些Endpoint协处理器执行一段Server端代码，并将Server端代码的结果返回给客户端处理。

Observer Coprocessor，这中协处理器类似于传统数据库中的触发器，当发生某些事件的时候，Observer协处理器会被Server端调用。

本篇文章先不介绍如何去开发协处理器，主要借助于HBase示例中自带的RowCount Endpoint协处理器来说明如何使用Java代码在客户端调用。在后面的文章Fayson会介绍如何去编写一个协处理器。

Endpoint Coprocessor客户端调用过程，如下图所示：

内容概述

1.环境准备

2.编写Java示例代码及运行

3.统计方式对比

测试环境

1.CM和CDH版本为5.14.3

2.环境准备

HBase中自带的Endpoint的协处理器，在hbase-examples.jar包中，在CDH的/opt/cloudera/parcels/CDH/lib/hbase/lib目录下，如下图所示：

1.确认hbase-examples-1.2.0-cdh5.14.2.jar是否在

详情请阅读原文：https://yq.aliyun.com/articles/670081
