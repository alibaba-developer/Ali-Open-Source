# HBase2.0 Observer的一些改动

最近需要把一个1.x的Observer移植到HBase2.0，发现2.0里面Observer接口和1.x相比还是有不少不一样的地方。所以简单在JIRA里面看了一下，把遇到的几个改动点的来龙去脉看了一下。从log来看，2.0里面Observer的调整还是比较多的，有一些是和其他特性相关联的改动，一篇文章很难整理的全面，所以这里只列出我遇到的一些改动。相信这些问题会有一定的代表性。

HBASE-17732 Coprocessor Design Improvements

https://issues.apache.org/jira/browse/HBASE-17732

在这个issue里面针对1.x里面coprocessor存在的一些问题，对coprocessor的设计做了大的调整。大部分的调整都不涉及接口，但是由于把Coprocessor和Observer之间的继承关系改成了组合关系，所以coprocessor的代码需要做相应的调整。

1.x Coprocessor存在的问题
由于CoprocessorEnvironment和他所对应的Observer类型并不是静态绑定的，所以CoprocessorHost不知道自己对应的Observer类型是哪个。这就导致了如下问题：
问题1:所有的host加载所有的observer，比如，MasterCoprocessorHost也会加载RegionObserver(其实只需要加载MasterObserver)。

问题2:host需要在运行时通过进行‘observer instanceOf ExpectedObserver’这样的检查来过滤掉不兼容的observer。
问题3:coprocessor host实现中存在大量重复的方法。

Observer和Coprocessor之间的继承关系导致如下问题：
问题4:如果第三方的coprocessor要使用多个observer，就需要在一个类里面派生所有用到的observer，导致这个类里面方法太多，类变得庞大，例如AccessController类有100多个方法：

解决办法
主要的改动有两个：

给CoprocessorEnvironment接口增加类型参数，变成CoprocessorEnvironment，这样每个CoprocessorHost里面只需要加载特定的Coprocessor即可。

引入4个coprocessor，并把coprocessor和Observer之间的关系由继承改为组合。

这样针对上面的问题1，CoprocessorHost可以通过CoprocessorEnvironment的类型参数和coprocessor的类型静态绑定，在加载时就可以忽略不匹配的类型。
针对问题2和问题3，新的实现里面把重复的方法都放在了CoprocessorHost里面做了实现。
针对问题4，把继承改为组合，同时在coprocessor里面提供了get方法来获得对应的Observer。

这里面，问题1，2，3其实都只涉及HBase内部运行机制，所对应的改动其实对coprocessor的开发也基本没有影响。真正对我们有影响的主要还是把继承改为组合。这样，之前的代码只需要继承Observer，现在则需要实现Observer和coprocessor两个接口。同时还需要实现coprocessor接口的getxxxObserver。

HBASE-17312 【JDK8】 Use default method for Observer Coprocessors

https://issues.apache.org/jira/browse/HBASE-17312

利用JDK8的新特性，给interface提供了默认实现，而不是像1.x里面放在Basexxx里面，这样对我们的影响是原来代码里面的
"Foo extends BaseXXXObserver"要改成"Foo implements XXXObserver"。

HBASE-18502 Change MasterObserver to use TableDescriptor and ColumnFamilyDescriptor

https://issues.apache.org/jira/browse/HBASE-18502

需要注意的是2.0里面TableDescriptor和HTableDescriptor的接口有一些地方不一样，需要针对性的做一些处理。

HBASE-18528 DON'T allow user to modify the passed table/column descriptor

https://issues.apache.org/jira/browse/HBASE-18528

如果之前有在coprocessor里面修改表或列族的一些属性的话，现在就需要重新设计了。

详情请阅读原文：https://yq.aliyun.com/articles/670124
