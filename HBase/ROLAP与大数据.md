# ROLAP与大数据

OLAP
大数据相关的场景比较多，常见的有：ETL（数据提取、转换、加载）、实时流式（监控报警、风控等）、机器学习（推荐引擎、用户画像等）、非结构化分析（视频、图片、语音、文本等）、海量大数据在线存储（HBase）、搜索及我们本文讲的OLAP。 其中OLAP（在线联机分析）在很多企业占住分析类的大部分。按照一般的理论又分为，M-OLAP，R-OLAP，H-OLAP。简单区别就是 M-OLAP是基于多维数组的存储模型，也是最原始的OLAP，但需要对数据进行预处理才能形成多维结构，大数据领域Kylin属于此类；R-OLAP是完全基于关系模型进行存放的，只是它根据分析的需要对模型的结构和组织形式进行了优化，目前MPP（greenplum）及MPP on HDFS(Presto、Impala)及SQL on DAG(Hive、Spark)均是此类。当然还有一类是H-OLAP，也就是说两者均满足需求，也是一种混合架构。

OLAP 与 大数据
大数据这个词，是从Hadoop之后才慢慢提出来的，开始的时候并不叫做大数据，很多企业叫做数据仓库部门，使用的基本是商用的组件。
随着Hadoop的兴起及MPP on廉价机器的发展，SQL on Hadoop类的引擎越来越多，比如有：Hive、Presto、Impala、Drill、Spark SQL、phoenix(SQL on HBase)、kylin(cube on HBase)等。特别是得益于分布式存储（HDFS）的发展，各类基于HDFS的分析组件越来越多，当然很多组件也支持HDFS之外的如：mysql、mongodb等数据源。下面笔者主要对比下目前主流R-OLAP模式的引擎，后续再有机会谈M-OLAP。
R-OLAP+大数据 也得益于列存储及内存存储的发展，再加上传统数据库的优化技术，在性能上也在不断加速。

R-OLAP

Hive On MR应该是最先支持P级别的分析引擎之一，由于实在太慢了，出现一大卡车的优化技术，主要体现在下面的一些产品中。
其中 Hive On Tez、Hive On Tez with LLAP、SparkSQL 、Presto、Phoenix 较为看好，具体如果单单做OLAP，Hive On tez最为合适，如果有其它分析类需求混合使用SQL，则Spark较为合适；Presto从理论上讲，应该较快，追求高性能；另外，如果数据存在Hbase中，想做一些简单的分析，可以使用phoenix。
对于Flink，看好StreamingSQL，不过OLAP一块，目前实在没有发力。
后续再分享M-OLAP的内容，OLAP基础理论参考google。

HBase技术交流社区 - 阿里官方“HBase生态+Spark社区大群”点击加入：https://dwz.cn/Fvqv066s

详情请阅读原文：https://yq.aliyun.com/articles/664733
