---
Create: 2022年 一月 17日, 星期一 23:03
tags: 
  - Engineering/hadoop
  - 大数据
---

## OutputFormat
OutputFormat是MapReduce输出的基类，所有实现MapReduce输出都实现了OutputFormat接口，下面是几种常见的OutputFormat常见实现类：
1. ==TextOutputFormat==：默认的输出格式是TextOutputFormat，它把每条记录写为文本行。它的键和值可以是任意类型，因为TextOutputFormat调用toString()方法把他们转换为字符串。
2. ==SequenceFileOutputFormat==：将SequenceFileOutputFormat输出作为后续MapReduce任务的输入，这是一种好的输出格式，因为它的格式紧凑，很容易被压缩。
3. 自定义OutputFormat：为了实现控制最终文件的输出路径和输出格式，可以自定义OutputFormat。例如，要在一个MapReduce程序中根据数据的不同输出两类结果到不同目录，这类灵活的输出需求可以通过自定义OutputFormat来实现。
	1. 自定义一个类继承FileOutputFormat。
	2. 改写RecordWriter，具体改写输出数据的方法write().




## 自定义OutputFormat
==需求==：过滤输入的log日志，包含baidu的网站输出到data/baidu.log，不包含atguigu的网站输出到data/other.log。
==输入数据==：
```
http://www.baidu.com
http:///ww.google..com
http://cn.bing.com
http://www.videos.com
http://www.sohu.com
http://www.sina.com
http://www.sin12a.com
http://www.sil12desa.com
http://www.sindscafa.com
```

==输出数据==：
```
baidu.log
	http://www.baidu.com
other.log
	http:///ww.google..com
	http://cn.bing.com
	http://www.videos.com
	http://www.sohu.com
	http://www.sina.com
	http://www.sin12a.com
	http://www.sil12desa.com
	http://www.sindscafa.com
```

==自定义一个OutputFormat类==：
创建一个FilterRecordWriter继承RecordWriter
1. 创建两个文件输出流：baiduOut，otherOut
2. 如果输入数据包含baidu，输出到baiduOut流，如果不包含baidu，输出到otherOut流

==驱动类Driver==：
```java
job.setOutputFormat(FilterOutputFormat.class)
```

### FilterMapper
```java
public class FilterMapper extends Mapper<LongWritable, Text, Text, NullWritable>{
	
	@Override
	protected void map(LongWritable key, Text value, Context context)	throws IOException, InterruptedException {

		// 写出
		context.write(value, NullWritable.get());
	}
}

```

### FilterReducer
```java
public class FilterReducer extends Reducer<Text, NullWritable, Text, NullWritable> {

	Text k = new Text();

	@Override
	protected void reduce(Text key, Iterable<NullWritable> values, Context context)		throws IOException, InterruptedException {

       // 1 获取一行
		String line = key.toString();

       // 2 拼接
		line = line + "\n";

       // 3 设置key
       k.set(line);

       // 4 输出
		context.write(k, NullWritable.get());
	}
}
```

### 自定义OutputFormat
```java
public class FilterOutputFormat extends FileOutputFormat<Text, NullWritable>{

	@Override
	public RecordWriter<Text, NullWritable> getRecordWriter(TaskAttemptContext job)			throws IOException, InterruptedException {

		// 创建一个RecordWriter
		return new FilterRecordWriter(job);
	}
}
```

### 自定义RecordWriter
```java
public class FilterRecordWriter extends RecordWriter<Text, NullWritable> {

	FSDataOutputStream baiduOut = null;
	FSDataOutputStream otherOut = null;

	public FilterRecordWriter(TaskAttemptContext job) {

		// 1 获取文件系统
		FileSystem fs;

		try {
			fs = FileSystem.get(job.getConfiguration());

			// 2 创建输出文件路径
			Path baiduPath = new Path("/home/lizhen/data/baidu.log");
			Path otherPath = new Path("/home/lizhen/data/other.log");

			// 3 创建输出流
			baiduOut = fs.create(atguiguPath);
			otherOut = fs.create(otherPath);
		} catch (IOException e) {
			e.printStackTrace();
		}
	}

	@Override
	public void write(Text key, NullWritable value) throws IOException, InterruptedException {

		// 判断是否包含“atguigu”输出到不同文件
		if (key.toString().contains("baidu")) {
			baiduOut.write(key.toString().getBytes());
		} else {
			otherOut.write(key.toString().getBytes());
		}
	}

	@Override
	public void close(TaskAttemptContext context) throws IOException, InterruptedException {

		// 关闭资源
		IOUtils.closeStream(atguiguOut);
		IOUtils.closeStream(otherOut);	
	}
}

```

### 自定义FilterDriver类
```java
public class FilterDriver {

	public static void main(String[] args) throws Exception {


		Configuration conf = new Configuration();
		Job job = Job.getInstance(conf);

		job.setJarByClass(FilterDriver.class);
		job.setMapperClass(FilterMapper.class);
		job.setReducerClass(FilterReducer.class);

		job.setMapOutputKeyClass(Text.class);
		job.setMapOutputValueClass(NullWritable.class);
		
		job.setOutputKeyClass(Text.class);
		job.setOutputValueClass(NullWritable.class);

		// 要将自定义的输出格式组件设置到job中
		job.setOutputFormatClass(FilterOutputFormat.class);

		FileInputFormat.setInputPaths(job, new Path(args[0]));

		// 虽然我们自定义了outputformat，但是因为我们的outputformat继承自fileoutputformat
		// 而fileoutputformat要输出一个_SUCCESS文件，所以，在这还得指定一个输出目录
		FileOutputFormat.setOutputPath(job, new Path(args[1]));

		boolean result = job.waitForCompletion(true);
		System.exit(result ? 0 : 1);
	}
}

```