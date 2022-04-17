---
Create: 2022年 四月 15日, 星期五 11:57
tags: 
  - Engineering/hive
  - 大数据
---


1. 创建一个Maven工程Hive
2. 导入依赖
	```
	<dependencies>
			<!-- https://mvnrepository.com/artifact/org.apache.hive/hive-exec -->
			<dependency>
				<groupId>org.apache.hive</groupId>
				<artifactId>hive-exec</artifactId>
				<version>3.1.2</version>
			</dependency>
	</dependencies>
	```

3. 创建一个类

	```
	package com.atguigu.hive;
	import org.apache.hadoop.hive.ql.exec.UDF;

	public class Lower extends UDF {

		public String evaluate (final String s) {

			if (s == null) {
				return null;
			}

			return s.toLowerCase();
		}
	}
	```
	
4. 打成jar包上传到服务器/opt/module/jars/udf.jar
5. 将jar包添加到hive的classpath `add jar /opt/module/datas/udf.jar;`
6. 创建临时函数与开发好的java class关联`create temporary function mylower as "com.atguigu.hive.Lower";`
7. 即可在hql中使用自定义的函数mylower  `select ename, mylower(ename) lowername from emp;`
