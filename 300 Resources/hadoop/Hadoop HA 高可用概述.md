---
Create: 2022年 一月 19日, 星期三 22:57
tags: 
  - Engineering/hadoop
  - 大数据
---

## 概述

1. 所谓HA（High Availablity），即高可用（7\*24小时不中断服务）。
2. 实现高可用最关键的策略是消除单点故障。HA严格来说应该分成各个组件的HA机制：HDFS的HA和YARN的HA。
3. Hadoop2.0之前，在HDFS集群中NameNode存在单点故障（SPOF）。
4. NameNode主要在以下两个方面影响HDFS集群
	1. NameNode机器发生意外，如宕机，集群将无法使用，直到管理员重启
	2. NameNode机器需要升级，包括软件、硬件升级，此时集群也将无法使用

HDFS HA功能通过配置Active/Standby两个NameNodes实现在集群中对NameNode的热备来解决上述问题。如果出现故障，如机器崩溃或机器需要升级维护，这时可通过此种方式将NameNode很快的切换到另外一台机器。






