# Hadoop黑客赎金事件解读及防范

年关将至，Mongodb数据丢失的事情还在眼前，数以千计的Mongodb数据库已经被删除或者被黑客勒索，参考:MongoDB黑客赎金事件解读及防范。就在最近一段时间，黑客也在攻击Hadoop，有不少Hadoop集群的数据全部丢失，这些数据甚至有上TB的数据量，对企业造成了巨大的损失。 本文讲述这个问题及后续的预防方案。

希望看到文章的同学及时修复问题，避免数据丢失。也欢迎转发，让更多的同学知晓。

攻击手段
一般使用者为了方便或者不注意，在电信IDC机房或者云上直接开放了HDFS的50070 web端口。那么黑客可以通过简单的几个命令，比如：

操作：

curl -i -X DELETE "http://ip:50070/webhdfs/v1/tmp?op=DELETE&recursive=true"
curl -i -X PUT "http://ip:50070/webhdfs/v1/NODATA4U_SECUREYOURSHIT?op=MKDIRS" 

黑客可以把tmp（可以其它目录）下的数据删除；也可以看到很多日志信息获取敏感数据；或者不断写，把HDFS写满；更加有一些黑客把数据备份走，再把HDFS删除，直接发送勒索邮件。

最后界面如图，一般黑客还提醒如下：

其实这个不是HDFS的漏洞，是HDFS提供的webhdfs功能，不过很多同学没有意识到数据可以通过此途径删除。
目前在 https://www.shodan.io 上查询50070端口，如下图

在中国有 8,500+ hadoop集群的50070端口是对公网开放的，这些集群理论都存在风险。这个只是此网站公布的，真实的数据远比这个多得多，安全形势不容乐观。
防护手段
基本的：
最好的处理方式是，把所有直接开放的端口，包括50070端口在内的所有端口对公网全部禁止。这些端口包括：
Hadoop默认情况开放了很多端口提供WebUI， 下面列举了相关端口信息：

HDFS

NameNode 默认端口 50070
SecondNameNode 默认端口 50090
DataNode 默认端口 50075
Backup/Checkpoint node 默认端口 50105-
YARN（JobTracker）

ResourceManager 默认端口8088
JobTracker 默认端口 50030
TaskTracker 默认端口 50060
Hue 默认端口 8080
等等

如果不清楚，则按照最小化原则，开放最小化端口，如果实在需要访问这些端口：
1、如果是云环境，可以在云上购买一台带界面的环境（win、linux等，这个机器就是跳板机），再最小化打通此机器跟Hadoop环境的通道。
2、即使要开通公网的端口，可以到ecs的安全组中，开通到你本地环境的公网ip的端口，不要全网开通。

PS：一些客户说我这个是测试环境，不是生产环境，丢失了没有关系。但是需要注意的是，如果客户攻击了你此台机器，此台机器沦为黑客的肉机不说，如果别的机器跟这些机器在一个安全组，则很有可能会攻击其他机器的。

高级的
关闭webhdfs的功能，dfs.webhdfs.enabled设置为false
开启类似kerberos功能（比较复杂，不过多表述）
及时备份重要数据，比如云上备份到OSS中
E-MapReduce
如果目前在ECS自建，或者想自建的同学，欢迎使用E-MapReduce产品：https://www.aliyun.com/product/emapreduce。 目前E-MapReduce产品购买集群后

默认会禁止所有大数据组件的所有端口。
yarn ui、ganglia等通过80端口访问，设置账号、密码访问机制，保证安全，无需直接开放50070端口。

详情请阅读原文：https://yq.aliyun.com/articles/664689
