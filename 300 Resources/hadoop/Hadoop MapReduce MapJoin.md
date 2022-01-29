---
Create: 2022年 一月 18日, 星期二 13:20
tags: 
  - Engineering/hadoop
  - 大数据
---


## Map Join
Map Join适用于一张表十分小、一张表很大的场景。
==优点==：
在Map端缓存多张表，提前处理业务逻辑，这样增加Map端业务，减少Reduce端数据的压力，尽可能的减少数据倾斜。

==具体方案==：
1. 在Mapper的setup阶段，将文件读取到缓存集合中。
2. 在驱动函数中加载缓存：
	```java
	// 缓存普通文件到Task运行节点。
	job.addCacheFile(new URI("file://home/lizhen/data/cache/pd.txt"));
	```


## Map Join 案例实操
订单表：

| id   | pid  | amount |
| ---- | ---- | ------ |
| 1001 | 01   | 1      |
| 1002 | 02   | 2      |
| 1003 | 03   | 3      |
| 1004 | 01   | 4      |
| 1005 | 02   | 5      |
| 1006 | 03   | 6      |

商品信息表：

| pid  | pname |
| ---- | ----- |
| 01   | 小米  |
| 02   | 华为  |
| 03   | 格力  |

==需求==：
将商品信息表中数据根据商品pid合并到订单数据表中。得到结果：

| id   | pname | amount |
| ---- | ----- | ------ |
| 1001 | 小米  | 1      |
| 1004 | 小米  | 4      |
| 1002 | 华为  | 2      |
| 1005 | 华为  | 5      |
| 1003 | 格力  | 3      |
| 1006 | 格力  | 6      |

==需求分析==：
1. DistributedCacheDrive缓存文件
	1. 加载缓存数据
		```java
		job.addCacheFile(new URI("file://home/lizhen/data/cache/pd.txt"));
		```
	2. Map端join的逻辑不需要Reduce阶段，设置ReduceTask数量为0
		```java
		job.setNumReduceTask(0);
		```
		
2. 读取缓存的文件数据
	1. setup()方法：
		1. 获取缓存文件
		2. 循环读取缓存文件一行
		3. 切割
		4. 缓存数据到集合
		5. 关流
	2. map方法：
		1. 获取一行
		2. 截取
		3. 获取订单id
		4. 获取商品名称
		5. 拼接
		6. 写出


## DistributedCacheDriver 
```java
public class DistributedCacheDriver {

	public static void main(String[] args) throws Exception {
		

		// 1 获取job信息
		Configuration configuration = new Configuration();
		Job job = Job.getInstance(configuration);

		// 2 设置加载jar包路径
		job.setJarByClass(DistributedCacheDriver.class);

		// 3 关联map
		job.setMapperClass(DistributedCacheMapper.class);
		
		// 4 设置最终输出数据类型
		job.setOutputKeyClass(Text.class);
		job.setOutputValueClass(NullWritable.class);

		// 5 设置输入输出路径
		FileInputFormat.setInputPaths(job, new Path(args[0]));
		FileOutputFormat.setOutputPath(job, new Path(args[1]));

		// 6 加载缓存数据
		job.addCacheFile(new URI("file://home/lizhen/data/cache/pd.txt"));
		
		// 7 Map端Join的逻辑不需要Reduce阶段，设置reduceTask数量为0
		job.setNumReduceTasks(0);

		// 8 提交
		boolean result = job.waitForCompletion(true);
		System.exit(result ? 0 : 1);
	}
}

```


## MjMapper 
```java

public class MjMapper extends Mapper<LongWritable, Text, Text, NullWritable> {

    //pd表在内存中的缓存
    private Map<String, String> pMap = new HashMap<>();
    private Text line = new Text();

    //任务开始前将pd数据缓存进PMap
    @Override
    protected void setup(Context context) throws IOException, InterruptedException {
        
        //从缓存文件中找到pd.txt
        URI[] cacheFiles = context.getCacheFiles();
        Path path = new Path(cacheFiles[0]);

        //获取文件系统并开流
        FileSystem fileSystem = FileSystem.get(context.getConfiguration());
        FSDataInputStream fsDataInputStream = fileSystem.open(path);

        //通过包装流转换为reader
        BufferedReader bufferedReader = new BufferedReader(
                new InputStreamReader(fsDataInputStream, "utf-8"));

        //逐行读取，按行处理
        String line;
        while (StringUtils.isNotEmpty(line = bufferedReader.readLine())) {
            String[] fields = line.split("\t");
            pMap.put(fields[0], fields[1]);
        }

        //关流
        IOUtils.closeStream(bufferedReader);

    }

    @Override
    protected void map(LongWritable key, Text value, Context context) throws IOException, InterruptedException {
        String[] fields = value.toString().split("\t");

        String pname = pMap.get(fields[1]);

        line.set(fields[0] + "\t" + pname + "\t" + fields[2]);

        context.write(line, NullWritable.get());
    }
}


```


