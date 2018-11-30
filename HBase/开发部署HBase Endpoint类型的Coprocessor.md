# HBase实操 | 开发部署HBase Endpoint类型的Coprocessor

1.文档编写目的

在前面的文章介绍了HBase自带的Coprocessor调用示例《如何使用Java调用HBase的 Endpoint Coprocessor》，本篇文章主要介绍如何开发一个HBase Endpoint类型的协处理器。

本篇文章示例协处理器主要实现了对列的Count、Max、Min、Sum以及Average。前面的文章调用Coprocessor定义的全局的，在本篇文章介绍另一种实现方式通过代码的方式对指定的表添加Coprocessor。

内容概述

1.环境准备

2.使用Protobuf生成序列化类

3.Endpoint Coprocessor服务端实现

4.Endpoint Coprocessor客户端实现

5.部署及调用

测试环境

1.CM和CDH版本为5.14.3

2.集群未启用Kerberos

2.环境准备

在HMaster、RegionServer内部，创建了RpcServer实例，并与Client三者之间实现了Rpc调用，在HBase0.95版本引入了Google-Protobuf作为中间数据组织方式，并在Protobuf提供的Rpc接口之上，实现了基于服务的Rpc实现。

Protobuf Buffers是一种轻便高效的结构化数据存储格式，可以用于数据序列化。适合做数据存储或RPC数据交换格式。用于通讯协议、数据存储等领域的语言无关、平台无关、可扩展的序列化结构数据格式。

这里Fayson借助于Protobuf来生成HBase RPC数据交换格式类，在HBase中使用的Protobuf版本为2.5.0，所以选择安装相同版本的Protobuf。

总结

在开发HBase的Coprocessor借助于Protobuf生成RPC请求数据交互类，我们只需要在生成的类基础上实现业务即可。

本篇文章主要介绍了怎么样通过代码的方式为指定的HBase表添加Coprocessor，这种方式使用更灵活，不需要重启HBase服务。

将编写好的Coprocessor jar上传至HDFS，确保文件的目录属主。

HBase自带的也有AggregateImplementation类实现列的聚合，原生的不能同时对多个列进行聚合处理，如果需要多次聚合则需要多次调用RPC请求，HBase数据在不断的写入会出现每次聚合的结果有偏差，本示例将聚合放在一个RPC中处理可以减少RPC的请求次数并确保查询条件相同的情况下不会出现数据不一致问题。

GitHub地址：

https://github.com/fayson/cdhproject/blob/master/hbasedemo/proto/MyFirstCoprocessor.proto

https://github.com/fayson/cdhproject/blob/master/hbasedemo/src/main/java/com/cloudera/hbase/coprocessor/server/MyFirstCoprocessorEndPoint.java

https://github.com/fayson/cdhproject/blob/master/hbasedemo/src/main/java/com/cloudera/hbase/coprocessor/server/MyFirstCoprocessor.java

https://github.com/fayson/cdhproject/blob/master/hbasedemo/src/main/java/com/cloudera/hbase/coprocessor/client/MyFirstCoprocessExample.java

详情请阅读原文：https://yq.aliyun.com/articles/670075
