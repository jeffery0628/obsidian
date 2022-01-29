---
Create: 2022年 一月 13日, 星期四 09:54
tags: 
  - Engineering/hadoop
  - 大数据
---
## 序列化概述
1. 什么是序列化？
	==序列化==：就是把内存中的对象，转换成字节序列（或其他数据传输协议）以便于存储到磁盘（持久化）和网络传输。
	==反序列化==：就是将收到字节序列（或其他数据传输协议）或者是磁盘的持久化数据，转换成内存中的对象。
2. 为什么要序列化？
	一般来说，“活的”对象只能生存在内存里，关机断电就没有了。而且“活的”对象只能由本地的进程使用，不能被发送到网络另一台计算机上。然而序列化可以存储“活的”对象，可以将“活的”对象发送到远程计算机。
3. 为什么不用java的序列化。
	java的序列化是一个重量级序列化框架，一个对象被序列化后，会附带很多额外的信息（各种校验信息，Header，继承体系等），不便于在网络中高效传输。所以，Hadoop自己开发了一套序列化机制（==Writable==）。
4. Hadoop序列化特点：
	1. 高效使用存储空间
	2. 读写数据的额外开销小
	3. 随着通信协议的升级而可升级
	4. 支持多语言的交互


## 自定义bean对象实现序列化接口（Writable）
在企业开发中往往常用的基本序列化类型不能满足所有需求，比如在Hadoop框架内部传递一个bean对象，那么该对象就需要实现序列化接口。
具体实现bean对象序列化步骤如下7步：
1. 必须实现Writable接口
2. 必须有空参构造函数，反序列化时，需要反射调用空参构造函数，所以必须有空参构造。
3. 重写序列化方法
4. 重写反序列化方法
5. ==注意反序列化的顺序和序列化的顺序完全一致==
6. 要想把结果显示在文件中，需要重写toString()，可用”\t”分开，方便后续用。
7. 如果需要将自定义的bean放在key中传输，则还需要实现Comparable接口，因为MapReduce框中的Shuffle过程要求对key必须能排序。


## 实操
### 需求
统计每一个手机号耗费的总上行流量、下行流量、总流量
输入数据格式：
```
7 	13560436666	120.196.100.99		1116		 954			200
id	手机号码		网络ip			上行流量  下行流量     网络状态码
```

期望输出数据格式:
```
13560436666 		1116		      954 			2070
手机号码		    上行流量        下行流量		总流量
```

### Map阶段
1. 读取一行数据，切分字段
	```
	7 	13560436666	120.196.100.99		1116		 954			200
	```
2. 提取手机号、上行流量、下行流量
	```
	13560436666		1116		 954
	手机号			  上行流量		下行流量
	```
3. 以手机号为Key，bean对象为value输出，即context.write(手机号,bean),bean对象要想能够传输，必须实现序列化接口

### Reduce阶段
1. 累加上行流量和下行流量得到总流量



## 代码
### 编写流量统计Bean类
```java
// 1 实现writable接口
public class FlowBean implements Writable {

    private long upFlow;
    private long downFlow;
    private long sumFlow;

    // 2. 反序列化，需要反射调用空参构造函数，所以必须有
    public FlowBean(){
        super();
    }

    public FlowBean(long upFlow,long downFlow){
        super();
        this.upFlow = upFlow;
        this.downFlow = downFlow;
        this.sumFlow = upFlow + downFlow;
    }


    //3. 写序列化方法
    @Override
    public void write(DataOutput dataOutput) throws IOException {
        dataOutput.writeLong(upFlow);
        dataOutput.writeLong(downFlow);
        dataOutput.writeLong(sumFlow);
    }

    //4. 反序列化方法：读顺序和写序列化方法的顺序必须一致
    @Override
    public void readFields(DataInput dataInput) throws IOException {
        this.upFlow = dataInput.readLong();
        this.downFlow = dataInput.readLong();
        this.sumFlow = dataInput.readLong();
    }

    @Override
    public String toString() {
        return upFlow +"\t" + downFlow +"\t" + sumFlow ;
    }

    public long getUpFlow() {
        return upFlow;
    }

    public void setUpFlow(long upFlow) {
        this.upFlow = upFlow;
    }

    public long getDownFlow() {
        return downFlow;
    }

    public void setDownFlow(long downFlow) {
        this.downFlow = downFlow;
    }

    public long getSumFlow() {
        return sumFlow;
    }

    public void setSumFlow(long sumFlow) {
        this.sumFlow = sumFlow;
    }
}
```
	
### 编写Mapper类
```java
public class FlowCountMapper extends Mapper<LongWritable, Text,Text,FlowBean> {
    FlowBean v = new FlowBean();
    Text k = new Text();


    @Override
    protected void map(LongWritable key, Text value, Context context) throws IOException, InterruptedException {
        // 1. 获取一行
        String line = value.toString();
        // 2. 切分一行字段
        String[] fields = line.split("\t");
        // 3. 封装对象
        String phoneNum = fields[1];
        long upFlow = Long.parseLong(fields[fields.length-3]);
        long downFlow = Long.parseLong(fields[fields.length-2]);
        
        k.set(phoneNum);
        v.set(upFlow,downFlow);
        
        // 4. 写出
        context.write(k,v);
    }
}

```

### 编写Reduce类
```java
public class FlowReducer extends Reducer<Text,FlowBean,Text,FlowBean> {
    @Override
    protected void reduce(Text key, Iterable<FlowBean> values, Context context) throws IOException, InterruptedException {
        long sumUpFlow = 0;
        long sumDownFlow = 0;
        
        // 1. 遍历所用bean，将其中的上行流量 下行流量分别累加
        for (FlowBean flowBean : values) {
            sumUpFlow += flowBean.getUpFlow();
            sumDownFlow += flowBean.getDownFlow();
        }
        
        // 2. 封装对象
        FlowBean resultBean = new FlowBean(sumUpFlow,sumDownFlow);
        
        // 3. 写出
        context.write(key,resultBean);
        
    }
}
```


### 编写Driver驱动类
```java
public class FlowSumDriver {
    public static void main(String[] args) throws IOException {
        // 1. 获取配置信息，或者job对象实例
        Configuration configuration = new Configuration();
        Job job = Job.getInstance(configuration);

        // 2. 指定本程序的jar包所在的本地路径
        job.setJarByClass(FlowSumDriver.class);

        // 3. 指定本业务需要的mapper/reducer业务类
        job.setMapperClass(FlowCountMapper.class);
        job.setReducerClass(FlowCountReducer.class);

        // 4. 指定mapper 输出 数据的key类型
        job.setMapOutputKeyClass(Text.class);
        job.setMapOutputValueClass(FlowBean.class);

        // 5. 指定最终 输出 的数据的kv类型
        job.setOutputKeyClass(Text.class);
        job.setOutputValueClass(FlowBean.class);

        // 6. 指定job的输入原始文件所在目录
        FileInputFormat.setInputPaths(job,new Path("src/main/resources/input"));
        FileOutputFormat.setOutputPath(job,new Path("src/main/resources/output"));

    }
}
```
