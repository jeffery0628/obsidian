---
Create: 2022年 三月 10日, 星期四 13:13
tags: 
  - Engineering/spark
  - 大数据
---
Spark Shell仅在测试和验证我们的程序时使用的较多，在生产环境中，通常会在IDE中编制程序，然后打成Jar包，然后提交到集群，最常用的是创建一个Maven项目，利用Maven来管理Jar包的依赖。

# 编写程序
1. 创建一个Maven项目
2. 输入文件夹准备：在新建的WordCount项目名称上右键新建input文件夹,在input文件夹上右键,分别新建1.txt和2.txt。每个文件里面准备一些word单词。
3. 导入项目依赖

	```bash
	<dependencies>
		<dependency>
			<groupId>org.apache.spark</groupId>
			<artifactId>spark-core_2.11</artifactId>
			<version>2.1.1</version>
		</dependency>
	</dependencies>
	<build>
		<finalName>WordCount</finalName>
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
4. 创建伴生对象WordCount，编写代码
	```scala
	import org.apache.spark.rdd.RDD
	import org.apache.spark.{SparkConf, SparkContext}

	object WordCount {

		def main(args: Array[String]): Unit = {

			//1.创建SparkConf并设置App名称
			val conf = new SparkConf().setAppName("WC").setMaster("local[*]")

			//2.创建SparkContext，该对象是提交Spark App的入口
			val sc = new SparkContext(conf)

			//3.读取指定位置文件:hello atguigu atguigu
			val lineRdd: RDD[String] = sc.textFile("input")

			//4.读取的一行一行的数据分解成一个一个的单词（扁平化）(hello)(atguigu)(atguigu)
			val wordRdd: RDD[String] = lineRdd.flatMap(line => line.split(" "))

			//5. 将数据转换结构：(hello,1)(atguigu,1)(atguigu,1)
			val wordToOneRdd: RDD[(String, Int)] = wordRdd.map(word => (word, 1))

			//6.将转换结构后的数据进行聚合处理 atguigu:1、1 =》1+1  (atguigu,2)
			val wordToSumRdd: RDD[(String, Int)] = wordToOneRdd.reduceByKey((v1, v2) => v1 + v2)

			//7.将统计结果采集到控制台打印
			val wordToCountArray: Array[(String, Int)] = wordToSumRdd.collect()
			wordToCountArray.foreach(println)

			//一行搞定
			//sc.textFile(args(0)).flatMap(_.split(" ")).map((_, 1)).reduceByKey(_ + _).saveAsTextFile(args(1))

			//8.关闭连接
			sc.stop()
		}
	}
	```

5. 打包插件
	```bash
	<plugin>
		<groupId>org.apache.maven.plugins</groupId>
		<artifactId>maven-assembly-plugin</artifactId>
		<version>3.0.0</version>
		<configuration>
			<archive>
				<manifest>
					<mainClass>com.atguigu.spark.WordCount</mainClass>
				</manifest>
			</archive>
			<descriptorRefs>
				<descriptorRef>jar-with-dependencies</descriptorRef>
			</descriptorRefs>
		</configuration>
		<executions>
			<execution>
				<id>make-assembly</id>
				<phase>package</phase>
				<goals>
					<goal>single</goal>
				</goals>
			</execution>
		</executions>
	</plugin>
	```

6. 打包到集群测试
	1. 点击package打包，然后，查看打完后的jar包
	2. 将WordCount.jar上传到/opt/module/spark目录
	3. 在HDFS上创建，存储输入文件的路径/input:`hadoop fs -mkdir /input`
	4. 上传输入文件到/input路径:`hadoop fs -put /opt/module/spark-local-standalone/input/1.txt /input`
	5. 执行任务:
		```bash
		bin/spark-submit \
		--class com.atguigu.spark.WordCount \
		--master yarn \
		WordCount.jar \
		/input \
		/output
		```
		
	6. 查询运行结果:`hadoop fs -cat /output/*`


# 本地调试
本地Spark程序调试需要使用Local提交模式，即将本机当做运行环境，Master和Worker都为本机。运行时直接加断点调试即可。如下：
```scala
import org.apache.spark.{SparkConf, SparkContext}

object WordCount {

    def main(args: Array[String]): Unit = {

        //1.创建SparkConf并设置App名称，设置本地模式运行
        val conf = new SparkConf().setAppName("WC").setMaster("local[*]")

        //2.创建SparkContext，该对象是提交Spark App的入口
        val sc = new SparkContext(conf)

        //3.使用sc创建RDD，输入和输出路径都是本地路径
        sc.textFile("input").flatMap(_.split(" ")).map((_, 1)).reduceByKey(_ + _).saveAsTextFile("output")

        //4.关闭连接
        sc.stop()
    }
}
```





