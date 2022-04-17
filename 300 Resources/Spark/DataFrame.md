---
Create: 2022年 三月 17日, 星期四 13:29
tags: 
  - Engineering/spark
  - 大数据
---



在Spark SQL中SparkSession是创建DataFrame和执行SQL的入口，创建DataFrame有三种方式：通过Spark的数据源进行创建；从一个存在的RDD进行转换；还可以从Hive Table进行查询返回。

# 创建DataFrame的三种方法
## 从Spark数据源进行创建
查看Spark支持创建文件的数据源格式：
```scala
scala> spark.read.
csv   format   jdbc   json   load   option   options   orc   parquet   schema   table   text   textFile
```

读取json文件创建DataFrame:
```scala
scala> val df = spark.read.json("/opt/module/spark-local/people.json")
df: org.apache.spark.sql.DataFrame = [age: bigint， name: string]
```
> 注意：如果从内存中获取数据，spark可以知道数据类型具体是什么，如果是数字，默认作为Int处理；但是从文件中读取的数字，不能确定是什么类型，所以用bigint接收，可以和Long类型转换，但是和Int不能进行转换

结果展示：
```
scala> df.show
+---+--------+
|age|    name|
+---+--------+
| 18|qiaofeng|
| 19|  duanyu|
| 20|    xuzhu|
+---+--------+
```

## 从RDD转换为DataFrame
如果需要RDD与DF或者DS之间操作，那么都需要引入 import spark.implicits._  （spark不是包名，而是sparkSession对象的名称，所以必须先创建SparkSession对象再导入. implicits是一个内部object）

前置条件
- 导入隐式转换并创建一个RDD
- 在/opt/module/spark-local/目录下准备people.txt
	```
	qiaofeng,18
	xuzhu,19
	duanyu,20 
	```
```scala
scala> import spark.implicits._
import spark.implicits._
---
scala> val peopleRDD = sc.textFile("/opt/module/spark-local/people.txt")
peopleRDD: org.apache.spark.rdd.RDD[String] = /opt/module/spark-local /people.txt MapPartitionsRDD[3] at textFile at <console>:27
```
### 通过手动确定转换
```scala
scala> peopleRDD.map{x=> val fields=x.split(",");(fields(0),fields(1).trim.toInt)}.toDF("name","age").show

+--------+---+
|    name|age|
+--------+---+
|qiaofeng| 18|
|   xuzhu| 19|
|  duanyu| 20|
+--------+---+
```
### 通过样例类反射转换
创建一个样例类：
```scala
scala> case class People(name:String,age:Int)
```
根据样例类将RDD转换为DataFrame
```scala
scala> peopleRDD.map{
	x=> var fields=x.split(",");
	People(fields(0),fields(1).toInt)}.toDF.show

+--------+---+
|    name|age|
+--------+---+
|qiaofeng| 18|
|   xuzhu| 19|
|  duanyu| 20|
+--------+---+
```
### 通过编程的方式
> 了解，一般编程直接操作RDD较少，操作hive或数据文件等较多
```scala
  def main(args: Array[String]): Unit = {
    val spark: SparkSession = SparkSession.builder()
      .master("local[*]")
      .appName("Word Count")
      .getOrCreate()
    val sc: SparkContext = spark.sparkContext
    val rdd: RDD[(String, Int)] = sc.parallelize(Array(("lisi", 10), ("zs", 20), ("zhiling", 40)))
    // 映射出来一个 RDD[Row], 因为 DataFrame其实就是 DataSet[Row]
    val rowRdd: RDD[Row] = rdd.map(x => Row(x._1, x._2))
    // 创建 StructType 类型
    val types = StructType(Array(StructField("name", StringType), StructField("age", IntegerType)))
    val df: DataFrame = spark.createDataFrame(rowRdd, types)
    df.show
  }
```
### 从DataFrame转换为RDD
直接调用rdd即可
1. 创建一个DataFrame
```scala
scala> val df = spark.read.json("/opt/module/spark-local/people.json")

df: org.apache.spark.sql.DataFrame = [age: bigint,name: string]
```
2. 将DataFrame转换为RDD注意：得到的RDD存储类型为Row
```scala
scala> val dfToRDD = df.rdd

dfToRDD: org.apache.spark.rdd.RDD[org.apache.spark.sql.Row] = MapPartitionsRDD[19] at rdd at <console>:29
```

3. 打印RDD

```scala
scala> dfToRDD.collect

res3: Array[org.apache.spark.sql.Row] = Array([18,qiaofeng], [19,duanyu], [20,xuzhu])
```

## 从Hive Table 进行查询返回




# SQL 风格语法
SQL语法风格是指我们查询数据的时候使用SQL语句来查询，这种风格的查询必须要有临时视图或者全局视图来辅助

1. 创建一个DataFrame:
	```scala
	scala> val df = spark.read.json("/opt/module/spark-local/people.json")

	df: org.apache.spark.sql.DataFrame = [age: bigint， name: string]
	```
2. 对DataFrame创建一个临时表:
	```scala
	scala> df.createOrReplaceTempView("people")
	```

3. 通过SQL语句实现查询全表:
	```scala
	scala> val sqlDF = spark.sql("SELECT * FROM people")

	sqlDF: org.apache.spark.sql.DataFrame = [age: bigint， name: string]
	```

4. 结果展示:
	```scala
	scala> sqlDF.show
	+---+--------+
	|age|    name|
	+---+--------+
	| 18|qiaofeng|
	| 19|  duanyu|
	| 20|   xuzhu|
	+---+--------+
	```
	>注意：普通临时表是Session范围内的，如果想应用范围内有效，可以使用全局临时表。使用全局临时表时需要全路径访问，如：global_temp.people

5. 对于DataFrame创建一个全局表:

	```scala
	scala> df.createGlobalTempView("people")
	```
6. 通过SQL语句实现查询全表:

	```scala
	scala> spark.sql("SELECT * FROM global_temp.people").show()
	+---+--------+
	|age|    name|
	+---+--------+
	| 18|qiaofeng|
	| 19|  duanyu|
	| 20|   xuzhu|
	+---+--------+

	scala> spark.newSession().sql("SELECT * FROM global_temp.people").show()
	+---+--------+
	|age|    name|
	+---+--------+
	| 18|qiaofeng|
	| 19|  duanyu|
	| 20|   xuzhu|
	+---+--------+
	```

# DSL风格语法
DataFrame提供一个特定领域语言去管理结构化的数据，可以在 Scala, Java, Python 和 R 中使用 DSL，使用 DSL 语法风格不必去创建临时视图了

1. 创建一个DataFrame
	```scala
	scala> val df = spark.read.json("/opt/module/spark-local /people.json")

	df: org.apache.spark.sql.DataFrame = [age: bigint， name: string]
	```
2. 查看DataFrame的Schema信息
	```scala
	scala> df.printSchema

	root
	 |-- age: Long (nullable = true)
	 |-- name: string (nullable = true)
	```
3. 只查看”name”列数据
	```scala
	scala> df.select("name").show()

	+--------+
	|    name|
	+--------+
	|qiaofeng|
	|  duanyu|
	|   xuzhu|
	+--------+
	```
4. 查看所有列
	```scala
	scala> df.select("*").show
	+--------+---------+
	|    name |age|
	+--------+---------+
	|qiaofeng|       18|
	|  duanyu|       19|
	|   xuzhu|       20|
	+--------+---------+
	```
5. 查看”name”列数据以及”age+1”数据 注意:涉及到运算的时候, 每列都必须使用\$
	```scala
	scala> df.select($"name",$"age" + 1).show
	+--------+---------+
	|    name|(age + 1)|
	+--------+---------+
	|qiaofeng|       19|
	|  duanyu|       20|
	|   xuzhu|       21|
	+--------+---------+
	```
6. 查看”age”大于”19”的数据
	```scala
	scala> df.filter($"age">19).show
	+---+-----+
	|age| name|
	+---+-----+
	| 20|xuzhu|
	+---+-----+
	```
7. 按照”age”分组，查看数据条数
	```scala
	scala> df.groupBy("age").count.show
	+---+-----+
	|age|count|
	+---+-----+
	| 19|    1|
	| 18|    1|
	| 20|    1|
	+---+-----+
	```
	
