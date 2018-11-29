# HBase 备份恢复

**Apsara HBase 备份恢复**

所有的数据库产品都有备份恢复，Apache HBase官方目前没有一个release版本的备份恢复功能，官网提出的方案和机制操作都是很复杂。所以阿里云赋能HBase的备份恢复能力并支持全量和增量的备份和恢复，同时具有高性能、低成本和低线上影响充分自动化。而且备份恢复是独立于HBase之外的模块，不影响HBase的正常使用，并且备份恢复模块有自有failover的能力，保证备份恢复的持续性。
1.独立于Apsara-HBase的备份恢复模块，提供独立的模块支撑
2.支持全量/增量恢复以及全量/增量恢复，高数据恢复精确度
3.全量/增量备份有failover模块保证数据安全备份
4.支持冷热分离等统一文件系统接口，并支同时持HBase的1.x、2.x版本的接口
5.数据备份到oss，拥有极高的数据可靠性，且存储成本低廉，oss上备份数据不会存在冗余的情况

**云HBase备份恢复原理
整体组成**

1.备份包括全量备份和增量备份，全量备份是在某个时刻的全量备份，增量备份是从某个时刻起的Hlog的备份，同时也会对两种备份数据压缩。
2.恢复也包括全量恢复和增量恢复，增量恢复是指从最近的全量恢复的时间点到指定的时间点的Hlog的增量恢复，全量恢复是指定时间点最近的一次全量备份数据恢复。

**相关指标**

1.全量备份最长时间限制是4天
2.全量恢复最长时间是1.5天
3.RPO(Recover Pointobjective)业务系统所能容忍的数据丢失量是1小时，二期会支持秒级
4.数据可靠性高达11个9（99.9999999%）且OSS存储成本极低
5.定期清理过期备份数据，可以降低备份数据的冗余

**备份部分：全量备份**

RS和MASTER的调度身份有所不同，如上图master节点会做snapshot的备份，RS节点做的是Hfile的备份，上转任务切分实现了两种方案 a) round robin近均匀策略 b) 基于short-circuit read的切分策略。使用failover机制保证失败重试，且基于Hfilelink，追踪hfile路径，保证读到数据。

**备份部分：增量备份**

正常情况下，各个hlogserver负责自己机器相关的hlog，并且实时收集备份hlog，备份精度在一小时以内。实现Hloglink，追踪hlog全链路的踪迹，保证读到数据；追踪WALs/oldWALs/splitting 3种状态，内存占用量只有20MB。 hlogserver采用了round robin takeover 策略保证不会漏备任何一条hlog。

当Hlogserver1服务和ecsdown机的时候，Hlogserver会把Hlogserver1当前的任务log13、log14转给Hlogserver2执行。如果Hlogserver1恢复服务的时候log1x相关的任务会继续在Hlogserver1上执行。

恢复部分

服务会根据需要的时间点恢复最近的一次全量备份和全量备份的时间点到需要恢复的时间点的增量Hlog备份。并且Hfile和Hlog的恢复都是各节点分布式执行的。

**阿里云HBase 备份恢复 vs 其他大数据数据库备份恢复**

下面的表格是阿里Hbase和其它Hbase备份恢复的对比
	
备份
	
恢复

阿里云HBase
	
全量备份，增量实时备份，保证备份精度，备份目标端保证低数据冗余
	
全量bulkload+log replay，可以恢复任意合理的集群规模

Apache HBase
	
社区方案需要依赖mr，且一次全量，以后都增量，增量备份精度有限，且存在数据冗余
	
全部hfile bulkload模式，速度较快。

Apache Cassandra
	
全量组合增量模式，可能引入多份冗余增量log的出口带宽
	
需要恢复到备份对等的集群规模

**文章来自：郭鹏——HBase生态+Spark社区大群 志愿者**

详情请阅读原文：https://yq.aliyun.com/articles/673235
