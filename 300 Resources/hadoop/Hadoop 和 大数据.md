---
Create: 2021年 十二月 26日, 星期日 16:33
tags: 
  - Engineering/java
  - 大数据
---

## hadoop 简介
### 是什么
1. Hadoop 是一个由Apache基金会开发的分布式系统基础架构。
2. 主要解决，海量数据的存储和海量数据的分析计算问题。
3. 广义上来说，hadoop 通常指一个更广泛的概念--hadoop生态圈。
![[700 Attachments/Pasted image 20211226163612.png]]


### 发展历史
1. Lucene框架是Doug Cutting开创的开源软件，用java书写代码，实现与Google类似的全文搜索功能，他提供了全文检索引擎的架构，包括完整的查询和索引引擎。
2. 2001年年底Lucene称为Apache基金会的一个子项目。
3. 对于海量数据的场景，Lucene面对与Google同样的困难，存储数据困难，检索速度慢。
4. 学习和模仿Google解决这些问题的版本：微型版Nutch
5. 可以说Google是Hadoop的思想之源（google在大数据方面的三篇论文）
	1. GFS --> HDFS
	2. Map-Reduce --> MR
	3. BigTable --> HBase

6. 2003-2004年，Google公开了部分GFS和MapReduce思想细节，以此为基础Doug Cutting等人用业余时间实现了DFS 和 MapReduce机制，使Nutch性能飙升。
7. 2005年Hadoop作为Lucene的子项目 Nutch的一部分正式引入Apache基金会
8. 2006年3月份，Map-Reduce和Nutch Distribute File System（NDFS）分别被纳入到Hadoop项目中，Hadoop就此正式诞生，标志着大数据时代来临。
9. 名字来源于Doug Cutting儿子的玩具大象


## Hadoop 三大发行版本
Hadoop三大发行版本：Apache、Cloudera、Hortonworks。
1. Apache版本最原始（最基础）的版本，对于入门学习最好。[官网](http://hadoop.apache.org/releases.html)、[下载地址](https://archive.apache.org/dist/hadoop/common/)
2. Cloudera内部集成了很多大数据框架。对应产品CDH。[官网](https://www.cloudera.com/downloads/cdh/5-10-0.html)、[下载地址](http://archive-primary.cloudera.com/cdh5/cdh/5/)
	1. 2008年成立的Cloudera是最早将Hadoop商用的公司，为合作伙伴提供Hadoop的商用解决方案，主要是包括支持、咨询服务、培训。
	2. 2009年Hadoop的创始人Doug Cutting也加盟Cloudera公司。Cloudera产品主要为CDH，Cloudera Manager，Cloudera Support
	3. CDH是Cloudera的Hadoop发行版，完全开源，比Apache Hadoop在兼容性，安全性，稳定性上有所增强。Cloudera的标价为每年每个节点10000美元。
	4. Cloudera Manager是集群的软件分发及管理监控平台，可以在几个小时内部署好一个Hadoop集群，并对集群的节点及服务进行实时监控。

3. Hortonworks文档较好。对应产品HDP。[官网](https://hortonworks.com/products/data-center/hdp/)、[下载地址](https://hortonworks.com/downloads/#data-platform)
	1. 2011年成立的Hortonworks是雅虎与硅谷风投公司Benchmark Capital合资组建。
	2. 公司成立之初就吸纳了大约25名至30名专门研究Hadoop的雅虎工程师，上述工程师均在2005年开始协助雅虎开发Hadoop，贡献了Hadoop80%的代码。
	3. Hortonworks的主打产品是Hortonworks Data Platform（HDP），也同样是100%开源的产品，HDP除常见的项目外还包括了Ambari，一款开源的安装和管理系统。
	4. Hortonworks目前已经被Cloudera公司收购。

## Hadoop 的优势（4高）
1. 高可靠性：Hadoop 底层维护多个数据副本，所以即使Hadoop某个计算元素或存储出现故障，也不会导致数据丢失
2. 高扩展性：在集群间分配任务数据，可方便的扩展数以千计的节点。
3. 高效性：在MapReduce的思想下，Hadoop是并行工作的，以加快任务处理速度。
4. 高容错性：能够自动将失败的任务重新分配。


