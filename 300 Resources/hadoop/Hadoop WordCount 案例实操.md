---
Create: 2022年 一月 12日, 星期三 13:29
tags: 
  - Engineering/hadoop
  - 大数据
---


![[700 Attachments/Pasted image 20220113103320.png]]
## 需求
在给定的文本文件中统计输出每一个单词出现的总次数。

### 需求分析
按照MapReduce编程规范，分别编写Mapper，Reducer，Driver。
1. 输入数据：
	```
	mt	mt
	ss	ss
	cls	cls
	jiao
	banzha
	xue
	hadoop	hadoop
	```
2. 输出数据：
	```
	mt	2
	banzha	1
	cls	2
	hadoop	2
	jiao	1
	ss	2
	xue	1
	```
3. Mapper
	1. 将MapTask传入的文本先转化成String
	2. 根据空格将一行切分成单词
	3. 将单词输出为\<单词,1\>
4. Reducer
	1. 汇总各个key的个数
	2. 输出key的总次数
5. Driver
	1. 获取配置信息，获取job对象实例
	2. 指定本程序的jar包所在的本地路径
	3. 关联Mapper/Reducer业务类
	4. 指定Mapper输出数据的kv类型
	5. 指定最终输出的数据的kv类型
	6. 指定job的输入原始文件所在目录
	7. 指定job的输出结果所在目录
	8. 提交作业

## 代码

### pom.xml
在项目的pom.xml文件中添加：
```xml
<dependencies>
	<dependency>
		<groupId>junit</groupId>
		<artifactId>junit</artifactId>
		<version>4.12</version>
	</dependency>
	<dependency> 
		<groupId>org.apache.logging.log4j</groupId>
		<artifactId>log4j-slf4j-impl</artifactId>
		<version>2.12.0</version> 
	</dependency> 
	<dependency> 
		<groupId>org.apache.hadoop</groupId>  
		<artifactId>hadoop-client</artifactId>  
		<version>3.1.3</version>  
	</dependency>
</dependencies>
```

### log4j2.xml
在项目的src/main/resources目录下，新建一个文件，命名为“log4j2.xml”
```xml
<?xml version="1.0" encoding="UTF-8"?>
<Configuration status="error" strict="true" name="XMLConfig">
    <Appenders>
        <!-- 类型名为Console，名称为必须属性 -->
        <Appender type="Console" name="STDOUT">
            <!-- 布局为PatternLayout的方式，
            输出样式为[INFO] [2018-01-22 17:34:01][org.test.Console]I'm here -->
            <Layout type="PatternLayout"
                    pattern="[%p] [%d{yyyy-MM-dd HH:mm:ss}][%c{10}]%m%n" />
        </Appender>

    </Appenders>

    <Loggers>
        <!-- 可加性为false -->
        <Logger name="test" level="info" additivity="false">
            <AppenderRef ref="STDOUT" />
        </Logger>

        <!-- root loggerConfig设置 -->
        <Root level="info">
            <AppenderRef ref="STDOUT" />
        </Root>
    </Loggers>

</Configuration>


```

### Mapper
```java

public class WordcountMapper extends Mapper<LongWritable, Text, Text, IntWritable> {
    Text k = new Text();
    IntWritable v = new IntWritable(1);

    @Override
    protected void map(LongWritable key, Text value, Context context) throws IOException, InterruptedException {

        // 1 获取一行
        String line = value.toString();

        // 2 切割
        String[] words = line.split("\t");

        // 3 输出
        for (String word : words) {

            k.set(word);
            context.write(k, v);
        }

    }
}
```

### Reducer

```java
public class WordcountReducer extends Reducer<Text, IntWritable, Text, IntWritable>{
    int sum;
    IntWritable v = new IntWritable();

    @Override
    protected void reduce(Text key, Iterable<IntWritable> values,Context context) throws IOException, InterruptedException {

        // 1 累加求和
        sum = 0;
        for (IntWritable count : values) {
            sum += count.get();
        }

        // 2 输出
        v.set(sum);
        context.write(key,v);
    }

}

```

### Driver
```java
public class WordcountDriver {
    public static void main(String[] args) throws IOException, ClassNotFoundException, InterruptedException {

        // 1 获取配置信息以及封装任务
        Configuration configuration = new Configuration();
        Job job = Job.getInstance(configuration);

        // 2 设置jar加载路径
        job.setJarByClass(WordcountDriver.class);

        // 3 设置map和reduce类
        job.setMapperClass(WordcountMapper.class);
        job.setReducerClass(WordcountReducer.class);

        // 4 设置map输出
        job.setMapOutputKeyClass(Text.class);
        job.setMapOutputValueClass(IntWritable.class);

        // 5 设置最终输出kv类型
        job.setOutputKeyClass(Text.class);
        job.setOutputValueClass(IntWritable.class);

        // 6 设置输入和输出路径
        FileInputFormat.setInputPaths(job, new Path("/Users/lizhen/IntellijProjects/HadoopPractise/src/main/resources/words.txt"));
        FileOutputFormat.setOutputPath(job, new Path("/Users/lizhen/IntellijProjects/HadoopPractise/src/main/resources/output"));

        // 7 提交
        boolean result = job.waitForCompletion(true);

        System.exit(result ? 0 : 1);
    }

}
```

## 在集群上测试
用maven打jar包，需要添加的打包插件依赖(pom.xml)。


```xml
<build>
		<plugins>
			<plugin>
				<artifactId>maven-compiler-plugin</artifactId>
				<version>2.3.2</version>
				<configuration>
					<source>1.8</source>
					<target>1.8</target>
				</configuration>
			</plugin>
			<plugin>
				<artifactId>maven-assembly-plugin </artifactId>
				<configuration>
					<descriptorRefs>
						<descriptorRef>jar-with-dependencies</descriptorRef>
					</descriptorRefs>
					<archive>
						<manifest>
							<mainClass>WordcountDriver</mainClass>
						</manifest>
					</archive>
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
		</plugins>
</build>


```
> 注意：
> 1. 自己工程主类
> 2. 如果工程上显示红叉。在项目上右键->maven->update project即可。

步骤：
1. 将程序打成jar包，然后拷贝到Hadoop集群中：右键->Run as->maven install。等待编译完成就会在项目的target文件夹中生成jar包。如果看不到。在项目上右键-》Refresh，即可看到。修改不带依赖的jar包名称为wc.jar，并拷贝该jar包到Hadoop集群。
2. 启动Hadoop集群
3. 执行WordCount程序：hadoop jar  wc.jar