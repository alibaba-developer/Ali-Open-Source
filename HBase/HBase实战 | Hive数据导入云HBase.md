# HBase实战 | Hive数据导入云HBase

网络环境
专线:用户需要把hbase集群的VPC相关网络信息配置到专线里面，可直通hbase环境

公有云虚拟机VPC环境：选择和hbase通VPC

其他：需要开hbase公网

注意：默认导入hbase数据，依赖的hbase-common、hbase-client、hbase-server、hbase-protocol使用社区的包即可。如果是公网需要使用云hbase发布的相关包

方案一：hive关联hbase表方式
适用场景：数据量不大4T以下(因为需要走hbase的api导入数据)

从hbase页面获取zk连接地址，并用下述方式启动hive客户端

hbase表不存在的情况

创建hive表hive_hbase_table映射hbase表base_table，会自动创建hbase表hbase_table,且会随着hive表删除而删除。这里需要指定hive的schema到hbase schema的映射关系。关于类型可参考Hive/HBaseIntegration

创建一张原始的hive表，准备一些数据

详情请阅读原文：https://yq.aliyun.com/articles/669255
