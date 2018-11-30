# E-MapReduce支持计算与存储分离,成本下降1倍

Hadoop一出生就是存储与计算在一起的，前几年面试题中都问，Hadoop怎么保证高性能呢？其中一个原因是存储不动，计算(code)动，不同于传统的集中式的存储模式。那我们为什么还要谈存储计算分离呢？众观历史，分久必合、合久必分，在计算机历史中也很类似，如今，也许到了计算与存储分离的阶段。后面我们以实际的case说明，分离的好处与劣势。

为什么呢？
先说一个笔者的，也应该是大家的经历：笔者家里的带宽是100mpbs，现在从来不保存电影，要看直接下载，基本几分钟就好了。这在几年前不可想象。
笔者也在《云上Hadoop之挑战》中分析了其中的挑战，其中有本地化的挑战：

带宽的速度，特别是机房内带宽的速度，已经从1000mps、2000mps、10000mps，甚至100000mpbs。但是磁盘的速度基本没有太大的变化。
因为硬件的变化，带来了软件架构的变化。
基本架构

架构其实比较简单，OSS作为默认的存储，Hadoop、Spark可以作为计算引擎直接分析OSS存储的数据。

以上比较了计算与存储分离的优缺点。
灵活性：在《E-MapReduce(Hadoop)10大类问题之集群规划》 一文中分析了集群规划问题，关键是匹配计算量与存储量，如果把计算与存储分离后，则 集群规划则变得简单很多，基本不需要估算未来业务的规模了，真正做到按需使用。
成本：存储与计算分离后。按照 1 master 8cpu32g 6 slave 8cpu32g 10T数据量 估算大致为，成本下降一倍。在ecs自建的磁盘选择 高效云盘。

性能：大约下降10%以内，对于一般的应用是可以接受的。后续详细说明。
场景测试及数据
测试的代码为：

https://github.com/fengshenwu/spark-terasort/tree/master/src/main/scala/com/github/ehiggs/spark/terasort

集群规模：1 master 4cpu 16g 、8 Slave 4cpu 16g、每个slave节点250G*4 高效云盘

测试spark脚本

 /opt/apps/spark-1.6.1-bin-hadoop2.7/bin/spark-submit  --master yarn --deploy-mode cluster --executor-memory 3G --num-executors 30    --conf spark.default.parallelism=800   --class  com.github.ehiggs.spark.terasort.TeraSort  spark-terasort-1.0-jar-with-dependencies.jar /data/teragen_100g /data/terasort_out_100g


详情请阅读原文：https://yq.aliyun.com/articles/664786
