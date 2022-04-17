---
Create: 2022年 四月 15日, 星期五 17:41
tags: 
  - Engineering/hive
  - 大数据
---

 

开启map输出阶段压缩可以减少job中map和Reduce task间数据传输量。具体配置如下：
1. 开启hive中间传输数据压缩功能`hive (default)>set hive.exec.compress.intermediate=true;`
2. 开启mapreduce中map输出压缩功能:`hive (default)>set mapreduce.map.output.compress=true;`
3. 设置mapreduce中map输出数据的压缩方式:`hive (default)>set mapreduce.map.output.compress.codec=org.apache.hadoop.io.compress.SnappyCodec;`
4. 执行查询语句:`hive (default)> select count(ename) name from emp;`



