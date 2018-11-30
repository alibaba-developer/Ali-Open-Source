# 2016年杭州第四次spark meetup见闻

## 引言
spark发展非常快，2.0都快要发布了。在不同的城市都会有一些交流聚会，杭州有很多互联网企业，创业的氛围也非常好。大家每隔一段时间的交流还是必须的。不过最好还是时间上密集点，最好重点在于大家讨论，互相交流。
这次是挖财网的时金魁同学主办的，感谢时金魁同学，[演讲稿下载链接](https://github.com/pusuo/spark_meetup/tree/master/hangzhou/4_20160605)

期待2.0发布，最近[E-MapReduce团队](https://www.aliyun.com/product/emapreduce?spm=5176.7960203.237031.19.N8ggG7)在做benchmark，找时间单独对比下1.6与2.0之间的性能差距。

## 过程

有4场次的分享，分别为：
-  《Spark 2.0介绍》来自七牛的陈超
大致介绍了spark2.0的一些新的功能，包括Dataset、Structured Streaming、Tungsten Phase2。记得最清楚就是 陈超说： 目前Structured Streaming还不成熟，吹了很多牛，大家得等到2.x版本吧，不管怎么搞还是基于batch，想跟flink一样估计到3.0，不过hadoop 3.0还没有发布，那spark也不知道啥时间了。
笔者路上堵车了，所以也只听到了一半。PS：陈超 别怪我写的不多，下面的图是借过来的。
现在网上分析2.0的文章比较多，笔者就不细讲了。

这个笔者是剧中人，还是比较熟悉的，经历过太多辛酸苦辣。念钧同学讲的比较好，从13年到16年虽然几度变化，但是还是在mllib上摸索。 此次讲了阿里mllib从13年到16年的一些事情，比如：机器学习算法平台的建设（可以拖的图形化界面，把算法包装好），在mllib踩过的一些坑（基本是数据量大以后的事情），MPI跟mllib的对比（mpi性能高，灵活性稳定性不够）。目前大数据方面，会有越来越多人从事大数据分析、数据挖掘上，这是一个热点。

- 《Spark+CarbonData(New File Format For Faster Data Analysis》来自华为陈亮，spark作为分析引擎可以基于CarbonData获取更快的查询性能。CarbonData是一个新的存储格式，跟parquet、orcfile比较类似。大致就是在列式存储的基础上加上编码、倒排等index的技术。看了 陈亮的演示，在100w条数据下，CarbonData比parquet快数倍的。不过现场有很多的问题，比如：写的性能怎么样？对于中文支持怎么样？cpu等有没有额外的开销？ 目前没有这方面的数据，最近进入了apache孵化器，期待后续有更加详细的数据。架构在于平衡，有利必有弊的。CarbonData 确实是一个很好的尝试，把传统DB的思想用来做大数据的数据存储格式也是很好的。 详细的信息见：[CarbonData](http://mp.weixin.qq.com/s?__biz=MzI4MzMzMTA1NQ==&mid=2247483688&idx=1&sn=337767eda1f7f52230072cb776439451&scene=2&srcid=06061MUADjZ2OoFkXf947N1P&from=timeline&isappinstalled=0#wechat_redirect)

- 《Spark Streaming简要图解》来自挖财网时金魁（现场照图片，比较可惜，补一张pdf里面封面，请时金魁见谅），介绍了挖财网用了spark+mesos+kafka+elasticsearch+kibana+Hbase，提到了Hbase作为数据存储服务扛不住sparkStreaming的压力，后开启了Hbase的反压，选择了mesos原因是在做隔离方面更好及Marathon对长服务支持更好，kibana对于展示metric比较好，最后图解了 Spark Streaming，看源码这样画一个图还是很方便理解的，比较赞成说代码写的比较乱，这快笔者也看过。

最后大家互相留下来，加了个微信，点个赞。

## 总结
此次会议有spark2.0、mllib、streaming及CarbonData，内容还是很丰富的。
出来参加spark meetup除了听分享外，最主要还是互相认识下，大家最好不要听完就走了。

## 版权声明
笔者微博：[阿里封神](http://weibo.com/fengshenwu?is_all=1) 欢迎转载，但请保留[原文地址](https://yq.aliyun.com/articles/53991)

## 社群
技术交流钉钉大群 阿里云 HBase+Spark社区 【强烈推荐！】 群内每周进行群直播技术分享及问答
- 加入方式1：点击link申请加入 https://dwz.cn/Fvqv066s

详情请阅读原文：https://yq.aliyun.com/articles/53991
