# HBase实操 | 使用Phoenix在CDH的HBase创建二级索引

1.文档编写目的

对于HBase而言，如果想精确地定位到某行记录，唯一的办法是通过rowkey来查询。如果不通过rowkey来查找数据，就必须逐行地比较每一列的值，即全表扫瞄。对于较大的表，全表扫描的代价是不可接受的。

但是，很多情况下，需要从多个角度查询数据。例如，在定位某个人的时候，可以通过姓名、身份证号、学籍号等不同的角度来查询，要想把这么多角度的数据都放到rowkey中几乎不可能(业务的灵活性不允许，对rowkey长度的要求也不允许)。

所以，需要secondary index来完成这件事。secondary index的原理很简单，即通过索引表来实现，但是如果自己维护的话则会麻烦一些。在很早的版本中，Phoenix就已经提供了对HBase secondary index的支持。

Fayson在前面的文章《Cloudera Labs中的Phoenix》和《如何在CDH中使用Phoenix》中介绍了Cloudera Labs中的Phoenix，以及如何在CDH5.11.2中安装和使用Phoenix4.7。本文Fayson主要介绍如何在CDH中使用Phoenix在HBase上建立二级索引。

内容概述：

1.建表与数据准备

2.启用Kafka的Sentry赋权

3.Kafka的赋权测试

4.总结

测试环境：

1.CM5.14.3/CDH5.14.2

2.Phoenix4.7.0

3.操作系统版本为Redhat7.4

4.采用root用户进行操作

5.集群未启用Kerberos

2.建表与数据准备

1.首先确保你的CDH集群已经安装Phoenix的Parcel，安装过程省略，具体可以参考《如何在CDH中使用Phoenix》。

详情请阅读原文：https://yq.aliyun.com/articles/670072
