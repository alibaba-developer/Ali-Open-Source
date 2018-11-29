# HBase实操 | 如何使用HBase存储图片

1.文档编写目的

Fayson在前面的文章中介绍了《如何使用HBase存储文本文件》和《如何使用Lily HBase Indexer对HBase中的数据在Solr中建立索引》，实现了文本文件保存到HBase中，并基于Solr实现了文本文件的全文检索。如果我们碰到的是图片文件呢，该如何保存或存储呢。本文主要描述如何将图片文件转成sequence file，然后保存到HBase。

内容概述

1.文件处理流程

2.准备上传文件的Java代码

3.运行代码

4.Hue中查询验证

测试环境

1.RedHat7.4

2.CM5.14.3

3.CDH5.14.2

4.集群未启用Kerberos

2.图片处理流程

1.如上图所示，Fayson先在本地准备了一堆图片文件，并上传到HDFS。

2.然后通过Java程序遍历所有图片生成一个Sequence File，然后把Sequence File入库到HBase，在入库过程中，我们读取图片文件的文件名作为Rowkey，另外将整个图片内容转为bytes存储在HBase表的一个column里。

3.最后可以通过Hue来进行查看图片，当然你也可以考虑对接到你自己的查询系统。

3.准备上传文件的Java代码

1.首先是准备Maven文件

2.注意修改代码中的配置项，如文本文件所在的HDFS目录，集群的Zookeeper地址等。将代码打成jar包并上传到集群服务器节点。该过程略。

3.准备执行脚本

2.注意修改代码中的配置项，如文本文件所在的HDFS目录，集群的Zookeeper地址等。将代码打成jar包并上传到集群服务器节点。该过程略。

3.准备执行脚本

详情请阅读原文：https://yq.aliyun.com/articles/670092
