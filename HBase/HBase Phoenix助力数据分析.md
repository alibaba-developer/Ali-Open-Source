# HBase Phoenix助力海量数据实时分析

phoenix这个项目我一直接触不多，在去年接触的一家公司使用phoenix分析数百亿的记录并且在秒级别返回的延迟时，笔者才慢慢探究一些phoenix的内幕。上个星期跟一位phoenix的PMC&Committer聊了聊phoenix的定位及未来的发展，发现phoenix还是比较竞争力的，从最近phoenix不断的发布版本来看，phoenix也在迅速的发展。在phoenix社区也组织了phoenixCon来宣传phoenix:PhoenixCon 2017。本文的目的是想梳理下phoenix的，phoenix怎么支持海量数据的实时分析，一些细节点不多。

架构
phoenix是专门为HBase增强的，具体说就是HBase有几个方面的问题:

HBase比较底层，没有SQL接口，对于希望使用SQL的同学转换成本高
HBase不支持二级索引(HBase自身也有一些方案，不过还是以phoenix支持最佳)
HBase不支持事务
最近几年，从SQL到NO-SQL，再到NewSQL的兴起。其实NewSQL本身并没有什么大技术的创新点(持久内存等硬件本身除外)，只是架构的创新或者说是一种混合架构。Phoenix是不是NewSQL，其实并不重要，重要的是他解决了什么问题。
大致的phoenix嵌入进HBase的图如下所示：

phoenix最主要给HBase添加了二级索引、SQL的支持。至于事物及一些其它的支持，算是锦上添花，让phoenix越看越是NewSQL。phoenix的官方文档比较全，笔者就不搬了。

secondary indexing：http://phoenix.apache.org/secondary_indexing.html
standard SQL：https://phoenix.apache.org/language/index.html
特别地：

支持join(hash joinSort-Merge Join)
相关SQL的优化(现在正在对接Calcite：http://calcite.apache.org/ )
支持jdbc直接访问
性能
phoenix适合海量数据的实时分析需求：

phoenix基本通过建立索引在海量数据上查询少量数据，并且基本实时返回。
phoenix也支持做一些复杂SQL操作，包括join，sub-query等。
phoenix不适合于ETL，比如10T数据变为10T数据。
如下有一张跟Hadoop体系hive的对比：

详情请阅读原文：https://yq.aliyun.com/articles/664670
