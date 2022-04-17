---
Create: 2022年 三月 17日, 星期四 10:20
tags: 
  - Engineering/spark
  - 大数据
---

可以很容易地在流数据上使用 DataFrames 和SQL，你必须使用SparkContext来创建StreamingContext要用的SQLContext。此外，这一过程可以在驱动失效后重启。我们通过创建一个实例化的SQLContext单实例来实现这个工作。
 

每个RDD被转换为DataFrame，以临时表格配置并用SQL进行查询。
```scala
val spark = SparkSession.builder.config(conf).getOrCreate()
import spark.implicits._
mapDS.foreachRDD(rdd =>{
  val df: DataFrame = rdd.toDF("word", "count")
  df.createOrReplaceTempView("words")
  spark.sql("select * from words").show
})

```



