---
Create: 2022年 四月 15日, 星期五 17:42
tags: 
  - Engineering/hive
  - 大数据
---
当Hive将输出写入到表中时，输出内容同样可以进行压缩。属性hive.exec.compress.output控制着这个功能。用户可能需要保持默认设置文件中的默认值false，这样默认的输出就是非压缩的纯文本文件了。用户可以通过在查询语句或执行脚本中设置这个值为true，来开启输出结果压缩功能。

案例：
1. 开启hive最终输出数据压缩功能`hive (default)>set hive.exec.compress.output=true;`
2. 开启mapreduce最终输出数据压缩:`hive (default)>set mapreduce.output.fileoutputformat.compress=true;`
3. 设置mapreduce最终数据输出压缩方式: `hive (default)> set mapreduce.output.fileoutputformat.compress.codec = org.apache.hadoop.io.compress.SnappyCodec;`
4. 设置mapreduce最终数据输出压缩为块压缩`hive (default)> set mapreduce.output.fileoutputformat.compress.type=BLOCK;`
5. 测试一下输出结果是否是压缩文件:
	```
	hive (default)> insert overwrite local directory
	 '/opt/module/datas/distribute-result' select * from emp distribute by deptno sort by empno desc;
	```



