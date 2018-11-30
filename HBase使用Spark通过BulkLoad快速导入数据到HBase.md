# HBase实操 | 使用Spark通过BulkLoad快速导入数据到HBase

HBase社区直播本期分享专家：明惠(网名：过往记忆)-阿里云数据架构师

视频地址：

https://yq.aliyun.com/live/590?spm=a2c4e.11155435.0.0.460177969kCLxf

PPT地址：

https://yq.aliyun.com/download/3033

PS：欢迎关注HBase+Spark团队号 https://yq.aliyun.com/teams/382 博客，问答，直播，各类HBase资料，线下meetup都会发布到这里。

1.文档编写目的

在项目中有需求需要将Hive表中的数据存储在HBase中。使用Spark访问Hive表，将读表数据导入到HBase中，写入HBase有两种方式：一种是通过HBase的API接口批量的将数据写入HBase，另一种是通过BulkLoad的方式生成HFile文件然后加载到HBase中，两种方式相比之下第二种效率会更高。本篇文章Fayson主要介绍如何使用Spark读取Hive表数据通过BulkLoad的方式快速的将数据导入到HBase。

文章概述

1.环境准备

2.示例代码及运行

3.总结

测试环境

1.CM5.14.3和CDH5.14.2

2.集群未启用Sentry和Kerberos

3.Spark1.6.0

2.环境准备

本篇文章主要使用HBase中hbase-spark包提供的HBaseContext来实现，需要准备hbase-spark的依赖包并部署到Spark集群。

1.将准备好的hbase-spark-1.2.0-cdh5.13.1.jar部署到集群所有节点的/opt/cloudera/parcels/CDH/lib/spark/lib目录下

详情请阅读原文：https://yq.aliyun.com/articles/669989
