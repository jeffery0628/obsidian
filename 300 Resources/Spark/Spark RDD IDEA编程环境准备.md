---
Create: 2022年 三月 11日, 星期五 10:15
tags: 
  - Engineering/spark
  - 大数据
---

1. 创建maven工程
2. 在pom文件中添加：

	```xml
	<dependencies>
		<dependency>
			<groupId>org.apache.spark</groupId>
			<artifactId>spark-core_2.11</artifactId>
			<version>2.1.1</version>
		</dependency>
	</dependencies>
	<build>
		<finalName>SparkCoreTest</finalName>
		<plugins>
			<plugin>
				<groupId>net.alchim31.maven</groupId>
				<artifactId>scala-maven-plugin</artifactId>
				<version>3.4.6</version>
				<executions>
					<execution>
						<goals>
							<goal>compile</goal>
							<goal>testCompile</goal>
						</goals>
					</execution>
				</executions>
			</plugin>
		</plugins>
	</build>
	```

3. 添加scala 框架支持 ![[700 Attachments/Pasted image 20220311101927.png]]
4. 创建一个scala文件夹，并把它修改为Source Root



