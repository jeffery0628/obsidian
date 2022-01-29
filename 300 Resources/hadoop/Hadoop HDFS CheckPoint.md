---
Create: 2022年 一月 16日, 星期日 16:17
tags: 
  - Engineering/hadoop
  - 大数据
---
通常情况下，SecondaryNameNode每隔一小时执行一次。该配置通过hdfs-default.xml中设置
```xml
<property>
  <name>dfs.namenode.checkpoint.period</name>
  <value>3600</value>
</property>

```

一分钟检查一次操作次数，当操作次数达到1百万时，SecondaryNameNode执行一次。
```xml
<property>
  <name>dfs.namenode.checkpoint.txns</name>
  <value>1000000</value>
<description>操作动作次数</description>
</property>

<property>
  <name>dfs.namenode.checkpoint.check.period</name>
  <value>60</value>
<description> 1分钟检查一次操作次数</description>
</property >


```


