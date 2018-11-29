# E-Mapreduce如何处理RDS的数据

一、引言
目前网站的一些业务数据存在数据库中，这些数据往往需要做进一步的分析，如：需要根据一些日志数据关联分析，或者需要进行一些如机器学习的分析。在阿里云上，目前E-Mapreduce能满足这种分析的需求。
在E-Mapredcue中操纵RDS中数据(这里以mysql为例)，一般有三种方式，下面分别说明下。
为了实验，笔者创建了一个mysql的示例，创建了一个数据库school，在其中创建一个表student，并导入了一部分的数据。

二、从mysql导入到oss中，再启用e-mapreduce分析
从mysql导入到oss中，可以使用 数据集成这个产品。

1、 使用数据集成 把数据导入到OSS中（都是页面配置）
进入数据集成的控制平台，创建一个Pipeline或者Ecs Pipeline
在新创建的Pipeline中，新建作业。目前没有直接的页视图，可以直接使用JSON视图
源类型选择Mysql，目标类型选择OSS，填写一些信息。再立即执行，脚本参考：

{
    "type": "job",
    "traceId": "stream to stream job test",
    "version": "1.0",
    "configuration": {
        "setting": {},
        "reader": {
            "plugin": "mysql",
            "parameter": {
                "instanceName": "rdst1234567890",
                "database": "school",
                "table": "student",
                "splitPk": "",
                "username": "hadoop",
                "password": "hadoop",
                "column": ["*"],
                "where": ""
            }
        },
        "writer": {
            "plugin": "oss",
            "parameter": {
                "endpoint": "http://oss-xxx",
                "accessId": "your accessId",
                "accessKey": "your accessKey",
                "bucket": "your bucket",
                "object": "your bucket，eg:   emr/school/student",
                "writeMode": "truncate",
                "encoding": "UTF-8",
                "nullFormat": "",
                "dateFormat": "",
                "fieldDelimiter": ","
            }
        }
    }
}

2、编写hive脚本
此部分可以参考文档：在Hive中使用OSS
特别如果想登陆到机器上执行hive脚本，一定要 sudo su hadoop到hadoop账户下或者新建一个账号。不要在root下操作。

三、通过sqoop导入mysql数据到HDFS中
目前E-Mapreduce镜像中没有直接提供sqoop，你可以登陆master机器，sudo su hadoop帐号下，再下载sqoop（master机器是可以访问互联网的）

下载sqoop及mysql-connector-java，目前列出的地址可以访问，如果不能访问，请直接访问官方下载。

//下载

wget http://apache.fayea.com/sqoop/1.4.6/sqoop-1.4.6.bin__hadoop-2.0.4-alpha.tar.gz
wget http://cdn.mysql.com//Downloads/Connector-J/mysql-connector-java-5.1.38.tar.gz

//解压

tar -xvf mysql-connector-java-5.1.38.tar.gz
tar -xvf sqoop-1.4.6.bin__hadoop-2.0.4-alpha.tar.gz

//把mysql-connector-java的jar拷贝到sqoop的lib中
cp mysql-connector-java-5.1.38/mysql-connector-java-5.1.38-bin.jar sqoop-1.4.6.bin__hadoop-2.0.4-alpha/lib/
执行命令，拷贝数据。sqoop有很多的用法，大家可以看下sqoop官方文档，或者google。
 ./sqoop-1.4.6.bin__hadoop-2.0.4-alpha/bin/sqoop import  --connect jdbc:mysql://rdsmcnlgxxxxxxx.mysql.rds.aliyuncs.com:3306/school --username yourMysqlusername--password yourMysqlPassword --table student --hive-import --hive-table school.student --target-dir student

查询下

hive> select count(*) from student;
Query ID = hadoop_20160408180707_a3326bcd-3a06-433c-94ba-002a29bb71ab
840
Time taken: 25.898 seconds, Fetched: 1 row(s)

四、可以直接连接mysql
这一步是需要写一些代码的，在代码中可以直接配置访问mysql。

package com.aliyun.emr;

import org.apache.spark.SparkConf;
import org.apache.spark.SparkContext;
import org.apache.spark.sql.DataFrameReader;
import org.apache.spark.sql.SQLContext;

/**
 * Hello world!
 */
public class VisitMysql {
    public static void main(String[] args) {
        String url = "jdbc:mysql://rdstxxxxxxxxxxxx.mysql.rds.aliyuncs.com:3306/school";
        SparkConf conf = new SparkConf();
        conf.setAppName("test");
        SparkContext sc = new SparkContext(conf);
        SQLContext sqlContext = new SQLContext(sc);
        DataFrameReader reader = sqlContext.read().format("jdbc");
        reader.option("url", url);
        reader.option("dbtable", "student");
        reader.option("driver", "com.mysql.jdbc.Driver");
        reader.option("user", "hadoop");
        reader.option("password", "hadoop");
        reader.load().show();
    }
}

<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/maven-v4_0_0.xsd">
  <modelVersion>4.0.0</modelVersion>
  <groupId>com.aliyun.emr</groupId>
  <artifactId>examples</artifactId>
  <packaging>jar</packaging>
  <version>1.0-SNAPSHOT</version>
  <name>examples</name>
  <url>http://maven.apache.org</url>
  <dependencies>
    <dependency>
      <groupId>org.apache.spark</groupId>
      <artifactId>spark-sql_2.10</artifactId>
      <version>1.6.0</version>
    </dependency>
    <dependency>
      <groupId>com.aliyun.emr</groupId>
      <artifactId>emr-sdk_2.10</artifactId>
      <version>1.1.0</version>
    </dependency>
    <dependency>
      <groupId>mysql</groupId>
      <artifactId>mysql-connector-java</artifactId>
      <version>5.1.30</version>
    </dependency>
    <dependency>
      <groupId>junit</groupId>
      <artifactId>junit</artifactId>
      <version>3.8.1</version>
      <scope>test</scope>
    </dependency>
  </dependencies>
</project>

注意
RDS默认是拒绝访问的，所以需要在RDS中配置网络白名单。在数据安全性->添加白名单分组。
如果是使用数据集成，则需要配置数据集成的IP地址，如果是使用sqoop或者直接相连，则需要配置E-Mapreduce的白名单(这个可以在集群的详情页看到)

HBase技术交流社区 - 阿里官方“HBase生态+Spark社区大群”点击加入：https://dwz.cn/Fvqv066s

详情请阅读原文：https://yq.aliyun.com/articles/664952
