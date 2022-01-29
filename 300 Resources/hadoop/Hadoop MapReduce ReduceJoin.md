---
Create: 2022年 一月 18日, 星期二 10:21
tags: 
  - Engineering/hadoop
  - 大数据
---

## Reduce Join 工作原理
==Map端的主要工作==：为来自不同表或文件的key/value对，打标签以区分不同来源的记录。然后用连接字段作为key，其余部分和新加的标志作为value，最后进行输出。
==Reduce端的主要工作==：在Reduce端以连接字段作为key的分组已经完成，我们只需要在每一个分组当中将那些来源不同文件的记录（在Map阶段已经打标志）分析，最后进行和并就ok了。


## Reduce Join 案例实操
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
通过将关联条件作为Map输出的key，将两表满足Join条件的数据并携带数据所来源的文件信息，发往同一个ReduceTask，在Reduce中进行数据的串联。

1. Map中处理的事情：
	1. 获取输入文件类型；
	2. 获取输入数据；
	3. 不同文件分别处理；
	4. 封装Bean对象输出。
2. 默认对产品pid排序

![[700 Attachments/Pasted image 20220118131128.png]]

![[700 Attachments/Pasted image 20220118131144.png]]

## OrderBean 
```java

public class OrderBean implements WritableComparable<OrderBean> {
    private String id;
    private String pid;
    private int amount;
    private String pname;

    @Override
    public String toString() {
        return id + "\t" + pname + "\t" + amount;
    }

    public String getId() {
        return id;
    }

    public void setId(String id) {
        this.id = id;
    }

    public String getPid() {
        return pid;
    }

    public void setPid(String pid) {
        this.pid = pid;
    }

    public int getAmount() {
        return amount;
    }

    public void setAmount(int amount) {
        this.amount = amount;
    }

    public String getPname() {
        return pname;
    }

    public void setPname(String pname) {
        this.pname = pname;
    }

    //按照Pid分组，组内按照pname排序，有pname的在前
    @Override
    public int compareTo(OrderBean o) {
        int compare = this.pid.compareTo(o.pid);
        if (compare == 0) {
            return o.getPname().compareTo(this.getPname());
        } else {
            return compare;
        }
    }

    @Override
    public void write(DataOutput out) throws IOException {
        out.writeUTF(id);
        out.writeUTF(pid);
        out.writeInt(amount);
        out.writeUTF(pname);
    }

    @Override
    public void readFields(DataInput in) throws IOException {
        id = in.readUTF();
        pid = in.readUTF();
        amount = in.readInt();
        pname = in.readUTF();
    }
}

```


## TableMapper
```java

public class OrderMapper extends Mapper<LongWritable, Text, OrderBean, NullWritable> {

    private String filename;
    private OrderBean order = new OrderBean();

    @Override
    protected void setup(Context context) throws IOException, InterruptedException {
        
        //获取切片文件名
        FileSplit fs = (FileSplit) context.getInputSplit();
        filename = fs.getPath().getName();
    }

    @Override
    protected void map(LongWritable key, Text value, Context context) throws IOException, InterruptedException {
        String[] fields = value.toString().split("\t");
        
        //对不同数据来源分开处理
        if ("order.txt".equals(filename)) {
            order.setId(fields[0]);
            order.setPid(fields[1]);
            order.setAmount(Integer.parseInt(fields[2]));
            order.setPname("");
        } else {
            order.setPid(fields[0]);
            order.setPname(fields[1]);
            order.setAmount(0);
            order.setId("");
        }

        context.write(order, NullWritable.get());
    }
}
```

## OrderReducer 
```java

public class OrderReducer extends Reducer<OrderBean, NullWritable, OrderBean, NullWritable> {

    @Override
    protected void reduce(OrderBean key, Iterable<NullWritable> values, Context context) throws IOException, InterruptedException {
        
        //第一条数据来自pd，之后全部来自order
        Iterator<NullWritable> iterator = values.iterator();
        
        //通过第一条数据获取pname
        iterator.next();
        String pname = key.getPname();
        
        //遍历剩下的数据，替换并写出
        while (iterator.hasNext()) {
            iterator.next();
            key.setPname(pname);
            context.write(key,NullWritable.get());
        }
    }
}

```

## TableDriver
```java

public class OrderDriver {
    public static void main(String[] args) throws IOException, ClassNotFoundException, InterruptedException {
        Job job = Job.getInstance(new Configuration());
        job.setJarByClass(OrderDriver.class);

        job.setMapperClass(OrderMapper.class);
        job.setReducerClass(OrderReducer.class);
        job.setGroupingComparatorClass(OrderComparator.class);

        job.setMapOutputKeyClass(OrderBean.class);
        job.setMapOutputValueClass(NullWritable.class);

        job.setOutputKeyClass(OrderBean.class);
        job.setOutputValueClass(NullWritable.class);

        FileInputFormat.setInputPaths(job, new Path("d:\\input"));
        FileOutputFormat.setOutputPath(job, new Path("d:\\output"));

        boolean b = job.waitForCompletion(true);

        System.exit(b ? 0 : 1);

    }
}
```

## 总结
这种方式，合并的操作是在Reduce阶段完成，Reduce端的处理压力太大，Map节点的运算负载则很低，资源利用率不高，且在Reduce阶段极易产生数据倾斜。

解决方案：Map端实现数据合并。