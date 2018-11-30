# 构建VPN满足云下开发环境访问云HBase数据库

当前云HBase正在公测中，不少客户在使用，在使用的过程中，一般开发同学在自己的电脑研发，需要在自己的电脑连接云HBase服务，对于性能等需求要求不高。本文主要讲述怎么通过VPN、VPC等方式构建一个测试环境，以满足开发的需求。

步骤
本文涉及的网络知识点比较多，可以事先看看VPC的文档。大致的架构如下所示：

服务端：一个云HBase集群、一个VPC网络（包含虚拟交换机）、一台安装了FlexGW的ecs。客户端：一个拨号软件Tunnelblick。接下来将分别从服务端及客户端来讲述整个流程。
服务端配置
选定一个region，这里以华北2为例子。

资源购买
VPC网络：创建一个VPC网络 HBase-VPC（专有网络），网段可以按照实际的情况选择A、B、C类网。

switch：在这个专有网络下，创建两个switch（hbase-switch-a，hbase-switch-c a、c代表不同的可用区）。一个switch给云HBase使用，另一个里面一会创建一个ECS实例。 这里也可以是一个switch。

ECS：购买一台带有FlexGW镜像的ecs实例hbase-vpn-flexgw（不要自带EIP），购买连接：https://market.aliyun.com/products/56812015/cmjj006981.html ，VPC网络选择hbase-switch-a
EIP：创建EIP，挂载到hbase-vpn-flexgw实例上（后续自由调整带宽大小）
云HBase：购买云HBase集群，再切换VPC到专有网络 HBase-VPC的hbase-switch-c下（这里可用区需要对应下）

详情请阅读原文：https://yq.aliyun.com/articles/664671
